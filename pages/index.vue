<template>
  <client-only>
    <div class="flex flex-col justify-around items-center h-screen bg-yellow-100">
      <div class="">
        <div class="p-5">
          <h1 class="text-4xl font-bold mb-8 ">2048 Game</h1>
          <div class="flex justify-between">
            <div class="flex flex-col justify-center score-board bg-gray-500 sm:w-[10vw] w-[30vw]   border rounded-lg">
              <div class="score-label text-sm text-center text-white ">Score</div>
              <div class="score-label text-xl text-center text-white ">{{ totalscore }}
                <span  class="fixed px-1 score-label text-xl text-center text-white getScore" v-if="isGetScore">
                  + {{ getScore }}
                </span>
              </div>
              
            </div>
            <button class="score-board bg-gray-800 text-xl p-4 text-white border rounded-lg" @click="resetGame()">
              New Game
            </button>
          </div>
          
          
        </div>
        <div class="p-3">
          <div class="grid grid-cols-4 relative bg-gray-600 rounded-lg">
            <div
              v-for="index in 16"
              :key="index"
              class="bg-gray-300 w-20 h-20 flex items-center justify-center text-2xl font-bold border rounded-lg m-1"
            >
            </div>
            <span
              v-for="tile in tiles"
              :key="tile.key"
                class="absolute flex items-center justify-center text-2xl font-bold rounded-lg m-1"
              :class="tileClass(tile.value)"
              :style="tileStyle(tile) "
            >
              {{ tile.value }}
            </span>
          </div>
        </div>
      </div>
      <div>

      </div>
    </div>
  </client-only>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

// 初始化
const tiles = ref([]);
const isGetScore = ref(false);
const getScore = ref(0);
const resetGame = () => {
  
  tiles.value = [];
  totalscore.value = 0;
  initializeGame();
  round.value = 0;
}

const totalscore = ref(0);
const round = ref(0);

// 新增數字格
const addNewTile = () => {
  const emptyCells = [];
  for (let y = 0; y < 4; y++) {
    for (let x = 0; x < 4; x++) {
      if (!tiles.value.some(tile => tile.x === x && tile.y === y)) {
        emptyCells.push({ x, y });
      }
    }
  }
  console.log('emptyCells:',emptyCells.length);
  if (emptyCells.length > 0) {
    console.log('check emptyCells ok!');
    for (let i = 0; i < 2; i++) {
      const newCell = emptyCells.splice(Math.floor(Math.random() * emptyCells.length), 1)[0];
      
      tiles.value.push({
        key: Date.now() + Math.random(),
        value: 2,
        x: newCell.x,
        y: newCell.y,
        animation: false
      });
    }
  }
};

// 鍵盤事件
const handleKeyDown = (event) => {
  let moved = false;
  switch (event.key) {
    case 'ArrowUp':
      moved = moveTiles(0, -1);
      break;
    case 'ArrowDown':
      moved = moveTiles(0, 1);
      break;
    case 'ArrowLeft':
      moved = moveTiles(-1, 0);
      break;
    case 'ArrowRight':
      moved = moveTiles(1, 0);
      break;
  }
  if (moved) {
    addNewTile();
    
  } else {
    console.log('No move');
  }

  if(!checkGameOver(1, 0) && !checkGameOver(-1, 0) && !checkGameOver(0, 1) && !checkGameOver(0, 1)){
    alert('Game Over');
  }
  if(checkGameWin()){
    alert('You Win!');
  }
  round.value++;
  console.log('Round:', round.value);
};

// 移動數字格
const moveTiles = (dx, dy) => {
  let moved = false;
  const size = 4;

  const getTile = (row, col) => tiles.value.find(t => t.x === col && t.y === row);
  
  const canMerge = (tile1, tile2) => tile1 && tile2 && tile1.value === tile2.value;
  const mergedPositions = new Set();
  const sortedTiles = tiles.value.slice().sort((a, b) => {
    if (dy === 1) return b.y - a.y;
    if (dy === -1) return a.y - b.y;
    if (dx === 1) return b.x - a.x;
    if (dx === -1) return a.x - b.x;
    return 0;
  });

  for (const tile of sortedTiles) {
    if(tile.value > 2){
      tile.animation = false;
    }
   
    let nx = tile.x;
    let ny = tile.y;
    while (true) {
      const nextX = nx + dx;
      const nextY = ny + dy;
      const nextTile = getTile(nextY, nextX);
      if (canMerge(tile, nextTile) && !mergedPositions.has(`${nextX},${nextY}`)) {
        nextTile.value *= 2;
        tiles.value = tiles.value.filter(t => t !== tile);
        mergedPositions.add(`${nextX},${nextY}`);
        moved = true;
        nextTile.animation = true;
        totalscore.value += nextTile.value;
        getScore.value = nextTile.value;
        isGetScore.value = true;
        setTimeout(() => {
          isGetScore.value = false;
          getScore.value = 0 ;
        }, 1000);
        break;
      } else if (!nextTile && nextX >= 0 && nextX < size && nextY >= 0 && nextY < size) {
        tile.x = nextX;
        tile.y = nextY;
        moved = true;
      } else {
        break;
      }
      nx = nextX;
      ny = nextY;
    }
  }
  return moved;
};

// 在组件挂载后添加键盘事件监听
onMounted(() => {
  window.addEventListener('keydown', handleKeyDown);
  document.addEventListener('touchstart', handleTouchStart);
  document.addEventListener('touchmove', handleTouchMove, { passive: false });
  document.addEventListener('touchend', handleTouchEnd);
  initializeGame();

});

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown);
  document.removeEventListener('touchstart', handleTouchStart);
  document.removeEventListener('touchmove', handleTouchMove);
  document.removeEventListener('touchend', handleTouchEnd);
});

const checkGameWin = () => {
  return tiles.value.some(tile => tile.value === 2048);
};

const checkGameOver = (dx, dy) => {
  
  let moved = false;
  const size = 4;
  const getTile = (row, col) => tiles.value.find(t => t.x === col && t.y === row);
  
  const canMerge = (tile1, tile2) => tile1 && tile2 && tile1.value === tile2.value;
  
  const mergedPositions = new Set();
  
  const sortedTiles = tiles.value.slice().sort((a, b) => {
    if (dy === 1) return b.y - a.y;
    if (dy === -1) return a.y - b.y;
    if (dx === 1) return b.x - a.x;
    if (dx === -1) return a.x - b.x;
    return 0;
  });

  for (const tile of sortedTiles) {
   
    let nx = tile.x;
    let ny = tile.y;
    while (true) {
      const nextX = nx + dx;
      const nextY = ny + dy;
      const nextTile = getTile(nextY, nextX);
      if (canMerge(tile, nextTile) && !mergedPositions.has(`${nextX},${nextY}`)) {
        moved = true;
        break;
      } else if (!nextTile && nextX >= 0 && nextX < size && nextY >= 0 && nextY < size) {
        moved = true;
      } else {
        break;
      }
      nx = nextX;
      ny = nextY;
    }
  }
  console.log('End checked:',moved);
  return moved;
};

// 處理移動格子的class
const tileClass = (value) => {
  return {
    'bg-yellow-200': value === 2,
    'bg-yellow-400': value === 4,
    'bg-yellow-600': value === 8,
    'bg-yellow-800': value === 16,
    'bg-red-400': value === 32,
    'bg-red-600': value === 64,
    'bg-green-400': value === 128,
    'bg-green-600': value === 256,
    'bg-blue-400': value === 512,
    'bg-blue-600': value === 1024,
    'bg-purple-400': value === 2048,
  };
};

// 动态样式绑定
const tileStyle = (tile) => {

  return {
    width: '5rem',
    height: '5rem',
    top: `${tile.y * 5.5}rem`,
    left: `${tile.x * 5.5}rem`,
    transition: 'top 0.2s, left 0.2s',
    animation: tile.animation ? `scale-up 0.3s ease-in-out, fade-in 0.3s ease-in-out 0.3s` : 'fade-in 0.3s ease-in-out',
  };
};

const initializeGame = () => {
  console.log('Game initialized');
  addNewTile();
  console.log(tiles.value);
};

watch(tiles, (newTiles) => {
  console.log('Tiles updated:', newTiles);
});

//行動裝置

const startX = ref(0);
const startY = ref(0);

const handleTouchStart = (event) => {
  const touch = event.touches[0];
  startX.value = touch.clientX;
  startY.value = touch.clientY;
  console.log(touch);
};

const handleTouchMove = (event) => {
  // 阻止默认行为以防止页面滚动
  event.preventDefault();
};

const handleTouchEnd = (event) => {
  const touch = event.changedTouches[0];
  const endX = touch.clientX;
  const endY = touch.clientY;

  const deltaX = endX - startX.value;
  const deltaY = endY - startY.value;

  if (Math.abs(deltaX) > Math.abs(deltaY)) {
    // 水平方向滑动
    if (deltaX > 0) {
      handleKeyDown({ key: 'ArrowRight' });
    } else {
      handleKeyDown({ key: 'ArrowLeft' });
    }
  } else {
    // 垂直方向滑动
    if (deltaY > 0) {
      handleKeyDown({ key: 'ArrowDown' });
    } else {
      handleKeyDown({ key: 'ArrowUp' });
    }
  }
};


</script>

<style scoped>
.grid {
  width: 22rem; /* 4 * (4rem + 0.5rem) */
  height: 22rem; /* 4 * (4rem + 0.5rem) */
}
.tile {
  position: absolute;
}

.getScore{
  animation: scale-up 0.3s ease-in-out;

}

</style>

<style>

@keyframes scale-up {
  0% {
    transform: scale(0.5);
  }
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes fade-in {
  0% {
    opacity: 0;
    transform: scale(0.5);
  }
  50% {

  }
  100% {
    opacity: 1;
    transition-duration: 0.3s;
    transform: scale(1);
}
  
}</style>