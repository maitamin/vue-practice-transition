<template>
  <div class="main">
    <button @click="myAnimation = 'slide'">Slide</button>
    <button @click="myAnimation = 'fade'">Fade</button>
    <br>
    {{ myAnimation }}
    <br>
    <button @click="add">追加</button>
    <ul style="width: 200px; margin: auto;">
      <transition-group name="fade">
        <li
          style="cursor: pointer;"
          v-for="(number, index) in numbers"
          :key="number"
          @click="remove(index)"
        >
          {{ number }}
        </li>
      </transition-group>
    </ul>
    <br>
    <button @click="show = !show">切り替え</button>
    <br>
    <br>
    <transition
      :css="false"
      @before-enter="beforeEnter"
      @enter="enter"
      @after-enter="afterEnter"
      @enter-cancelled="enterCancelled"

      @before-leave="beforeLeave"
      @leave="leave"
      @after-leave="afterLeave"
      @leave-cancelled="leaveCancelled"

    >
      <div class="circle" v-if="show"></div>
    </transition>
    <br>
    <button @click="myComponent = 'ComponentA'">ComponentA</button>
    <button @click="myComponent = 'ComponentB'">ComponentB</button>
    <transition name="fade" mode="out-in">
      <component :is="myComponent"></component>
    </transition>

    <transition name="fade" mode="out-in">
      <p v-if="show" key="bye">さようなら</p>
      <p v-if="!show" key="hello">こんにちは</p>
    </transition>
    <transition 
      enter-class=""
      enter-active-class="animate__animated animate__bounce"
      enter-to-class=""
      leave-class=""
      leave-active-class="animate__animated animate__shakeX"
      leave-to-class=""
      appear
      >
      <p v-if="show">hello</p>
    </transition>
    <transition :name="myAnimation" appear>
      <p v-if="show">bye</p>
    </transition>
  </div>
</template>

<script>
import ComponentA from "./components/ComponentA";
import ComponentB from "./components/ComponentB";

export default {
  components: {
    ComponentA,
    ComponentB,
  },
  data() {
    return {
      numbers: [0, 1, 2],
      nextNumber: 3,
      show: true,
      myAnimation: "slide",
      myComponent: "ComponentA",
    };
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.numbers.length);
    },
    add() {
      this.numbers.splice(this.randomIndex(), 0, this.nextNumber);
      this.nextNumber += 1;
    },
    remove(index) {
      this.numbers.splice(index, 1);
    },
    beforeEnter(el) {
      // 現れる前
      console.log('beforeEnter');

      el.style.transform = 'scale(0)';
    },
    enter(el, done) {
      // 現れる時
      console.log('enter');

      let scale = 0;
      const interval = setInterval(() => {
        el.style.transform = `scale(${scale})`;
        scale += 0.1;
        if (scale > 1) {
          clearInterval(interval);
          done();
        }
      }, 20);
    },
    afterEnter(el) {
      // 現れた後
      console.log('afterEnter');
    },
    enterCancelled(el) {
      // 現れるアニメーションがキャンセルされた時
      console.log('enterCancelled');
    },
    beforeLeave(el) {
      // 消える前
      console.log('beforeLeave');
    },
    leave(el, done) {
      // 消える時
      console.log('leave');

      let scale = 1;
      const interval = setInterval(() => {
        el.style.transform = `scale(${scale})`;
        scale -= 0.1;
        if (scale < 0) {
          clearInterval(interval);
          done();
        }
      }, 20);
    },
    afterLeave(el) {
      // 消えた後
      console.log('afterLeave');
    },
    leaveCancelled(el) {
      // 消えるアニメーションがキャンセルされた時
      // v-showの時だけ有効
      console.log('leaveCancelled');
    },
  }
}
</script>

<style scoped>
.circle {
  width: 200px;
  height: 200px;
  margin: auto;
  border-radius: 100px;
  background-color: deeppink;
}

.fade-move {
  transition: transform 1s;
}
.fade-enter {
  /* 現れる時の最初の状態 */
  opacity: 0;
}
.fade-enter-active {
  /* 現れる時のトランジションの状態 */
  transition: opacity 0.5s;
}
.fade-enter-to {
  /* 現れる時の最後の状態 */
  opacity: 1;
}
.fade-leave {
  /* 消える時の最初の状態 */
  opacity: 1;
} 
.fade-leave-active {
  /* 消える時のトランジションの状態 */
  transition: opacity 0.5s;
  position: absolute;
  width: 200px;
}
.fade-leave-to {
  /* 消える時の最後の状態 */
  opacity: 0;
}

.slide-enter,
.slide-leave-to {
  opacity: 0;

}
.slide-enter-active {
  animation: slide-in 0.5s;
  transition: opacity 1s;
}
.slide-leave-active {
  animation: slide-in 0.5s reverse;
  transition: opacity 1s;
}

@keyframes slide-in {
  from {
    transform: translateX(100px);
  }
  to {
    transform: translateX(0);
  }
}

.main {
  width: 70%;
  margin: auto;
  padding-top: 5rem;
  text-align: center;
}
</style>
