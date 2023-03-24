<template>
  <div>居中布局</div>
  <div class="father">
    <div class="son"></div>
  </div>
</template>

<style lang="scss" scoped>
.father {
  width: 500px;
  height: 500px;
  border: 1px solid #ddd;
}
.son {
  width: 40px;
  height: 40px;
  background-color: skyblue;
}

// flex
/* .father {
  display: flex;
  align-items: center;
  justify-content: center;
} */

// 绝对定位配合margin:auto,的实现方案
/* .father {
  position: relative;
}
.son {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
} */

// 绝对定位配合transform实现
.father {
  position: relative;
}
.son {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
