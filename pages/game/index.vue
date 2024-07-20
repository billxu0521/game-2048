<template>
  <client-only>
    <div class="flex justify-center items-center h-screen">
      <div class="grid grid-cols-4 relative">
        <div
          v-for="index in 16"
          :key="index"
          class="bg-gray-300 w-16 h-16 flex items-center justify-center text-2xl font-bold border rounded-lg"
        >
        </div>
        <span
          v-for="tile in tiles"
          :key="tile.key"
            class="absolute flex items-center justify-center text-2xl font-bold rounded-lg"
          :class="tileClass(tile.value)"
          :style="tileStyle(tile) "
        >
          {{ tile.value }}
        </span>
      </div>
    </div>
  </client-only>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

// 初始化数字格子数组
const tiles = ref([]);

const resetTiles = () => {
  tiles.value = [];
}

// 添加新的数字格子
const addNewTile = () => {
  const emptyCells = [];
  for (let y = 0; y < 4; y++) {
    for (let x = 0; x < 4; x++) {
      if (!tiles.value.some(tile => tile.x === x && tile.y === y)) {
        emptyCells.push({ x, y });
      }
    }
  }
  
  if (emptyCells.length > 0) {
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

// 处理键盘事件
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
    console.log(checkGameOver());
    if (checkGameOver()) {
      alert('Game Over');
    }
  } else {
    console.log('No move');
  }
};

// 移动数字格子
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
        console.log('merged:',nextTile);
        nextTile.animation = true;
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
  initializeGame();
});

const checkGameOver = () => {
  const getTile = (row, col) => tiles.value.find(t => t.x === col && t.y === row);

  for (let y = 0; y < 4; y++) {
    for (let x = 0; x < 4; x++) {
      const tile = getTile(y, x);
      if (!tile) return false; // 还有空的格子
      if ((x < 3 && tile.value === getTile(y, x + 1)?.value) || 
          (y < 3 && tile.value === getTile(y + 1, x)?.value)) {
        return false; // 还有可以合并的格子
      }
    }
  }
  return true; // 没有可以移动或合并的格子
};

// 动态类绑定
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
    width: '4rem',
    height: '4rem',
    top: `${tile.y * 4.5}rem`,
    left: `${tile.x * 4.5}rem`,
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
  // Perform any necessary actions when the tiles value changes
  console.log('Tiles updated:', newTiles);
});


</script>

<style scoped>
.grid {
  width: 18rem; /* 4 * (4rem + 0.5rem) */
  height: 18rem; /* 4 * (4rem + 0.5rem) */
}
.tile {
  position: absolute;
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