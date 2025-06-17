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
.game-container {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  user-select: none;
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
  gap: 15px;
}

.score-container {
  display: flex;
  gap: 15px;
}

.score-box {
  background: #bbada0;
  border-radius: 6px;
  padding: 10px 15px;
  color: white;
  text-align: center;
  min-width: 80px;
}

.score-label {
  font-size: 12px;
  text-transform: uppercase;
  margin-bottom: 5px;
}

.score-value {
  font-size: 18px;
  font-weight: bold;
}

.game-board {
  background: #bbada0;
  border-radius: 10px;
  padding: 10px;
  position: relative;
  touch-action: none;
}

.game-row {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

.game-row:last-child {
  margin-bottom: 0;
}

.game-cell {
  width: 70px;
  height: 70px;
  background: rgba(238, 228, 218, 0.35);
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  transition: all 0.15s ease-in-out;
}

.cell-content {
  font-size: 24px;
  font-weight: bold;
  color: #776e65;
}

/* 不同数值的方块样式 */
.cell-2 {
  background: #eee4da;
  color: #776e65;
}

.cell-4 {
  background: #ede0c8;
  color: #776e65;
}

.cell-8 {
  background: #f2b179;
  color: #f9f6f2;
}

.cell-16 {
  background: #f59563;
  color: #f9f6f2;
}

.cell-32 {
  background: #f67c5f;
  color: #f9f6f2;
}

.cell-64 {
  background: #f65e3b;
  color: #f9f6f2;
}

.cell-128 {
  background: #edcf72;
  color: #f9f6f2;
  font-size: 20px;
}

.cell-256 {
  background: #edcc61;
  color: #f9f6f2;
  font-size: 20px;
}

.cell-512 {
  background: #edc850;
  color: #f9f6f2;
  font-size: 20px;
}

.cell-1024 {
  background: #edc53f;
  color: #f9f6f2;
  font-size: 18px;
}

.cell-2048 {
  background: #edc22e;
  color: #f9f6f2;
  font-size: 18px;
  box-shadow: 0 0 10px rgba(237, 194, 46, 0.5);
}

.game-instructions {
  margin-top: 20px;
  text-align: center;
  color: #776e65;
  font-size: 14px;
}

.game-instructions p {
  margin: 5px 0;
}

/* 响应式设计 */
@media (max-width: 480px) {
  .game-container {
    padding: 15px;
  }
  
  .game-cell {
    width: 60px;
    height: 60px;
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
  
  .score-box {
    min-width: 70px;
    padding: 8px 12px;
  }
  
  .score-value {
    font-size: 16px;
  }
}

@media (max-width: 360px) {
  .game-cell {
    width: 50px;
    height: 50px;
  }
  
  .cell-content {
    font-size: 18px;
  }
  
  .cell-128 .cell-content,
  .cell-256 .cell-content,
  .cell-512 .cell-content {
    font-size: 14px;
  }
  
  .cell-1024 .cell-content,
  .cell-2048 .cell-content {
    font-size: 12px;
  }
}
</style>
