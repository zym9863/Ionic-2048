<template>
  <div class="game-container">
    <!-- 游戏头部信息 -->
    <div class="game-header">
      <div class="score-container">
        <div class="score-box">
          <div class="score-label">分数</div>
          <div class="score-value">{{ score }}</div>
        </div>
        <div class="score-box">
          <div class="score-label">最高分</div>
          <div class="score-value">{{ bestScore }}</div>
        </div>
      </div>
      <div class="game-controls">
        <ion-button @click="resetGame" color="primary" size="small">
          <ion-icon :icon="refreshOutline" slot="start"></ion-icon>
          重新开始
        </ion-button>
      </div>
    </div>

    <!-- 游戏面板 -->
    <div class="game-board" @touchstart="handleTouchStart" @touchend="handleTouchEnd">
      <div 
        v-for="(row, rowIndex) in board" 
        :key="rowIndex" 
        class="game-row"
      >
        <div 
          v-for="(cell, colIndex) in row" 
          :key="colIndex" 
          class="game-cell"
          :class="getCellClass(cell)"
        >
          <div v-if="cell !== 0" class="cell-content">
            {{ cell }}
          </div>
        </div>
      </div>
    </div>

    <!-- 游戏结束弹窗 -->
    <ion-alert
      :is-open="gameOver"
      header="游戏结束"
      :message="isWin ? '恭喜你达到了2048！' : '游戏结束！'"
      :buttons="gameOverButtons"
      @didDismiss="gameOver = false"
    ></ion-alert>

    <!-- 游戏说明 -->
    <div class="game-instructions">
      <p>使用箭头键或滑动来移动方块</p>
      <p>相同数字的方块会合并成一个</p>
      <p>目标是创造出2048方块！</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { IonButton, IonIcon, IonAlert } from '@ionic/vue'
import { refreshOutline } from 'ionicons/icons'

// 游戏状态
const board = ref<number[][]>([])
const score = ref(0)
const bestScore = ref(0)
const gameOver = ref(false)
const isWin = ref(false)

// 触摸事件处理
const touchStartX = ref(0)
const touchStartY = ref(0)
const minSwipeDistance = 50

// 初始化游戏板
const initBoard = () => {
  board.value = Array(4).fill(null).map(() => Array(4).fill(0))
  addRandomTile()
  addRandomTile()
}

// 添加随机方块
const addRandomTile = () => {
  const emptyCells: Array<[number, number]> = []
  
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      if (board.value[i][j] === 0) {
        emptyCells.push([i, j])
      }
    }
  }
  
  if (emptyCells.length > 0) {
    const randomIndex = Math.floor(Math.random() * emptyCells.length)
    const [row, col] = emptyCells[randomIndex]
    board.value[row][col] = Math.random() < 0.9 ? 2 : 4
  }
}

// 移动逻辑
const moveLeft = (): boolean => {
  let moved = false
  const newBoard = board.value.map(row => [...row])
  
  for (let i = 0; i < 4; i++) {
    const row = newBoard[i].filter(cell => cell !== 0)
    
    // 合并相同的方块
    for (let j = 0; j < row.length - 1; j++) {
      if (row[j] === row[j + 1]) {
        row[j] *= 2
        score.value += row[j]
        row.splice(j + 1, 1)
        
        if (row[j] === 2048 && !isWin.value) {
          isWin.value = true
          gameOver.value = true
        }
      }
    }
    
    // 填充空格
    while (row.length < 4) {
      row.push(0)
    }
    
    // 检查是否有移动
    for (let j = 0; j < 4; j++) {
      if (newBoard[i][j] !== row[j]) {
        moved = true
      }
    }
    
    newBoard[i] = row
  }
  
  if (moved) {
    board.value = newBoard
  }
  
  return moved
}

const moveRight = (): boolean => {
  let moved = false
  const newBoard = board.value.map(row => [...row])
  
  for (let i = 0; i < 4; i++) {
    const row = newBoard[i].filter(cell => cell !== 0)
    
    // 合并相同的方块（从右到左）
    for (let j = row.length - 1; j > 0; j--) {
      if (row[j] === row[j - 1]) {
        row[j] *= 2
        score.value += row[j]
        row.splice(j - 1, 1)
        
        if (row[j] === 2048 && !isWin.value) {
          isWin.value = true
          gameOver.value = true
        }
      }
    }
    
    // 填充空格（在左边）
    while (row.length < 4) {
      row.unshift(0)
    }
    
    // 检查是否有移动
    for (let j = 0; j < 4; j++) {
      if (newBoard[i][j] !== row[j]) {
        moved = true
      }
    }
    
    newBoard[i] = row
  }
  
  if (moved) {
    board.value = newBoard
  }
  
  return moved
}

const moveUp = (): boolean => {
  let moved = false
  const newBoard = board.value.map(row => [...row])
  
  for (let j = 0; j < 4; j++) {
    const column = []
    for (let i = 0; i < 4; i++) {
      if (newBoard[i][j] !== 0) {
        column.push(newBoard[i][j])
      }
    }
    
    // 合并相同的方块
    for (let i = 0; i < column.length - 1; i++) {
      if (column[i] === column[i + 1]) {
        column[i] *= 2
        score.value += column[i]
        column.splice(i + 1, 1)
        
        if (column[i] === 2048 && !isWin.value) {
          isWin.value = true
          gameOver.value = true
        }
      }
    }
    
    // 填充空格
    while (column.length < 4) {
      column.push(0)
    }
    
    // 检查是否有移动并更新列
    for (let i = 0; i < 4; i++) {
      if (newBoard[i][j] !== column[i]) {
        moved = true
      }
      newBoard[i][j] = column[i]
    }
  }
  
  if (moved) {
    board.value = newBoard
  }
  
  return moved
}

const moveDown = (): boolean => {
  let moved = false
  const newBoard = board.value.map(row => [...row])
  
  for (let j = 0; j < 4; j++) {
    const column = []
    for (let i = 0; i < 4; i++) {
      if (newBoard[i][j] !== 0) {
        column.push(newBoard[i][j])
      }
    }
    
    // 合并相同的方块（从下到上）
    for (let i = column.length - 1; i > 0; i--) {
      if (column[i] === column[i - 1]) {
        column[i] *= 2
        score.value += column[i]
        column.splice(i - 1, 1)
        
        if (column[i] === 2048 && !isWin.value) {
          isWin.value = true
          gameOver.value = true
        }
      }
    }
    
    // 填充空格（在上边）
    while (column.length < 4) {
      column.unshift(0)
    }
    
    // 检查是否有移动并更新列
    for (let i = 0; i < 4; i++) {
      if (newBoard[i][j] !== column[i]) {
        moved = true
      }
      newBoard[i][j] = column[i]
    }
  }
  
  if (moved) {
    board.value = newBoard
  }
  
  return moved
}

// 检查游戏是否结束
const checkGameOver = () => {
  // 检查是否还有空格
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      if (board.value[i][j] === 0) {
        return false
      }
    }
  }
  
  // 检查是否还能合并
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      const current = board.value[i][j]
      if ((i < 3 && board.value[i + 1][j] === current) ||
          (j < 3 && board.value[i][j + 1] === current)) {
        return false
      }
    }
  }
  
  return true
}

// 处理移动
const handleMove = (direction: string) => {
  if (gameOver.value) return
  
  let moved = false
  
  switch (direction) {
    case 'left':
      moved = moveLeft()
      break
    case 'right':
      moved = moveRight()
      break
    case 'up':
      moved = moveUp()
      break
    case 'down':
      moved = moveDown()
      break
  }
  
  if (moved) {
    addRandomTile()
    updateBestScore()
    
    if (checkGameOver()) {
      gameOver.value = true
    }
  }
}

// 键盘事件处理
const handleKeyPress = (event: KeyboardEvent) => {
  switch (event.key) {
    case 'ArrowLeft':
      event.preventDefault()
      handleMove('left')
      break
    case 'ArrowRight':
      event.preventDefault()
      handleMove('right')
      break
    case 'ArrowUp':
      event.preventDefault()
      handleMove('up')
      break
    case 'ArrowDown':
      event.preventDefault()
      handleMove('down')
      break
  }
}

// 触摸事件处理
const handleTouchStart = (event: TouchEvent) => {
  touchStartX.value = event.touches[0].clientX
  touchStartY.value = event.touches[0].clientY
}

const handleTouchEnd = (event: TouchEvent) => {
  if (!touchStartX.value || !touchStartY.value) return
  
  const touchEndX = event.changedTouches[0].clientX
  const touchEndY = event.changedTouches[0].clientY
  
  const deltaX = touchEndX - touchStartX.value
  const deltaY = touchEndY - touchStartY.value
  
  const absDeltaX = Math.abs(deltaX)
  const absDeltaY = Math.abs(deltaY)
  
  if (Math.max(absDeltaX, absDeltaY) < minSwipeDistance) return
  
  if (absDeltaX > absDeltaY) {
    // 水平滑动
    if (deltaX > 0) {
      handleMove('right')
    } else {
      handleMove('left')
    }
  } else {
    // 垂直滑动
    if (deltaY > 0) {
      handleMove('down')
    } else {
      handleMove('up')
    }
  }
}

// 获取方块样式类
const getCellClass = (value: number) => {
  if (value === 0) return 'cell-empty'
  return `cell-${value}`
}

// 更新最高分
const updateBestScore = () => {
  if (score.value > bestScore.value) {
    bestScore.value = score.value
    localStorage.setItem('2048-best-score', bestScore.value.toString())
  }
}

// 重置游戏
const resetGame = () => {
  score.value = 0
  gameOver.value = false
  isWin.value = false
  initBoard()
}

// 游戏结束按钮
const gameOverButtons = [
  {
    text: '重新开始',
    handler: () => {
      resetGame()
    }
  }
]

// 生命周期
onMounted(() => {
  // 加载最高分
  const savedBestScore = localStorage.getItem('2048-best-score')
  if (savedBestScore) {
    bestScore.value = parseInt(savedBestScore)
  }
  
  // 初始化游戏
  initBoard()
  
  // 添加键盘事件监听
  document.addEventListener('keydown', handleKeyPress)
})

onUnmounted(() => {
  document.removeEventListener('keydown', handleKeyPress)
})
</script>

<style scoped>
/* 全局样式变量 */
:root {
  --game-bg-gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  --game-bg-secondary: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
  --cell-empty-bg: rgba(255, 255, 255, 0.1);
  --cell-border-radius: 12px;
  --shadow-soft: 0 8px 32px rgba(31, 38, 135, 0.37);
  --shadow-strong: 0 12px 40px rgba(0, 0, 0, 0.2);
  --backdrop-blur: blur(4px);
  --transition-smooth: all 0.3s ease;
  --transition-bounce: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.game-container {
  max-width: 520px;
  margin: 0 auto;
  padding: 30px 20px;
  user-select: none;
  background: var(--game-bg-gradient);
  border-radius: 24px;
  box-shadow: var(--shadow-strong);
  backdrop-filter: var(--backdrop-blur);
  border: 1px solid rgba(255, 255, 255, 0.18);
  position: relative;
  overflow: hidden;
}

.game-container::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, transparent, rgba(255, 255, 255, 0.05), transparent);
  pointer-events: none;
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
  flex-wrap: wrap;
  gap: 20px;
  position: relative;
  z-index: 1;
}

.score-container {
  display: flex;
  gap: 16px;
}

.score-box {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: var(--backdrop-blur);
  border-radius: var(--cell-border-radius);
  padding: 14px 18px;
  color: white;
  text-align: center;
  min-width: 90px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  transition: var(--transition-smooth);
}

.score-box:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.15);
}

.score-label {
  font-size: 11px;
  text-transform: uppercase;
  margin-bottom: 6px;
  font-weight: 600;
  letter-spacing: 0.5px;
  opacity: 0.9;
}

.score-value {
  font-size: 20px;
  font-weight: 700;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.game-controls ion-button {
  --background: rgba(255, 255, 255, 0.2);
  --color: white;
  --border-radius: 12px;
  --box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  --padding-start: 16px;
  --padding-end: 16px;
  font-weight: 600;
  transition: var(--transition-bounce);
}

.game-controls ion-button:hover {
  --background: rgba(255, 255, 255, 0.3);
  transform: translateY(-2px) scale(1.05);
}

.game-board {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: var(--backdrop-blur);
  border-radius: 20px;
  padding: 16px;
  position: relative;
  touch-action: none;
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: inset 0 4px 16px rgba(0, 0, 0, 0.1);
}

.game-row {
  display: flex;
  gap: 12px;
  margin-bottom: 12px;
}

.game-row:last-child {
  margin-bottom: 0;
}

.game-cell {
  width: 76px;
  height: 76px;
  background: var(--cell-empty-bg);
  border-radius: var(--cell-border-radius);
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  transition: var(--transition-bounce);
  border: 1px solid rgba(255, 255, 255, 0.1);
  backdrop-filter: var(--backdrop-blur);
}

.cell-content {
  font-size: 26px;
  font-weight: 800;
  color: #2c3e50;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: var(--transition-smooth);
}

.game-cell:not(.cell-empty) {
  animation: cellAppear 0.3s ease-out;
}

@keyframes cellAppear {
  0% {
    transform: scale(0);
    opacity: 0;
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

/* 现代化的方块配色方案 */
.cell-2 {
  background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
  color: #2c3e50;
  box-shadow: 0 4px 12px rgba(168, 237, 234, 0.4);
}

.cell-4 {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);
  color: #2c3e50;
  box-shadow: 0 4px 12px rgba(252, 182, 159, 0.4);
}

.cell-8 {
  background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(255, 154, 158, 0.4);
}

.cell-16 {
  background: linear-gradient(135deg, #ffeaa7 0%, #fab1a0 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(255, 234, 167, 0.4);
}

.cell-32 {
  background: linear-gradient(135deg, #fd79a8 0%, #fdcb6e 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(253, 121, 168, 0.4);
}

.cell-64 {
  background: linear-gradient(135deg, #e17055 0%, #f39c12 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(225, 112, 85, 0.4);
}

.cell-128 {
  background: linear-gradient(135deg, #00b894 0%, #00cec9 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(0, 184, 148, 0.4);
  font-size: 22px;
}

.cell-256 {
  background: linear-gradient(135deg, #6c5ce7 0%, #a29bfe 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(108, 92, 231, 0.4);
  font-size: 22px;
}

.cell-512 {
  background: linear-gradient(135deg, #fd79a8 0%, #e84393 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(253, 121, 168, 0.4);
  font-size: 22px;
}

.cell-1024 {
  background: linear-gradient(135deg, #00b894 0%, #55a3ff 100%);
  color: white;
  box-shadow: 0 6px 16px rgba(0, 184, 148, 0.5);
  font-size: 20px;
}

.cell-2048 {
  background: linear-gradient(135deg, #ffeaa7 0%, #f39c12 100%);
  color: white;
  font-size: 20px;
  box-shadow: 0 8px 24px rgba(243, 156, 18, 0.6);
  animation: pulse2048 2s infinite;
}

@keyframes pulse2048 {
  0%, 100% {
    transform: scale(1);
    box-shadow: 0 8px 24px rgba(243, 156, 18, 0.6);
  }
  50% {
    transform: scale(1.05);
    box-shadow: 0 12px 32px rgba(243, 156, 18, 0.8);
  }
}

.game-instructions {
  margin-top: 25px;
  text-align: center;
  color: rgba(255, 255, 255, 0.9);
  font-size: 15px;
  line-height: 1.6;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: var(--backdrop-blur);
  padding: 16px;
  border-radius: var(--cell-border-radius);
  border: 1px solid rgba(255, 255, 255, 0.15);
}

.game-instructions p {
  margin: 8px 0;
  font-weight: 500;
}

/* 响应式设计 */
@media (max-width: 480px) {
  .game-container {
    padding: 20px 15px;
    margin: 10px;
    border-radius: 20px;
  }
  
  .game-cell {
    width: 64px;
    height: 64px;
  }
  
  .cell-content {
    font-size: 22px;
  }
  
  .cell-128 .cell-content,
  .cell-256 .cell-content,
  .cell-512 .cell-content {
    font-size: 18px;
  }
  
  .cell-1024 .cell-content,
  .cell-2048 .cell-content {
    font-size: 16px;
  }
  
  .score-box {
    min-width: 75px;
    padding: 12px 14px;
  }
  
  .score-value {
    font-size: 18px;
  }
  
  .game-header {
    gap: 15px;
    margin-bottom: 20px;
  }
}

@media (max-width: 360px) {
  .game-cell {
    width: 54px;
    height: 54px;
  }
  
  .cell-content {
    font-size: 20px;
  }
  
  .cell-128 .cell-content,
  .cell-256 .cell-content,
  .cell-512 .cell-content {
    font-size: 16px;
  }
  
  .cell-1024 .cell-content,
  .cell-2048 .cell-content {
    font-size: 14px;
  }
  
  .game-board {
    padding: 12px;
  }
  
  .game-row {
    gap: 8px;
    margin-bottom: 8px;
  }
}

/* 添加方块移动动画类 */
.cell-move {
  transition: all 0.2s ease-in-out;
}

.cell-merge {
  animation: merge 0.3s ease-out;
}

@keyframes merge {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}

/* 新方块出现动画 */
.cell-new {
  animation: newTile 0.3s ease-out;
}

@keyframes newTile {
  0% {
    transform: scale(0) rotate(45deg);
    opacity: 0;
  }
  50% {
    transform: scale(1.1) rotate(22.5deg);
    opacity: 0.8;
  }
  100% {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
}
</style>
