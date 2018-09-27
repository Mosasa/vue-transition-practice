<template>
  <div id="app">
     <transition :name="transitionName">
      <router-view class="view app-view"></router-view>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      transitionName: 'fold-left'
    }
  },
  watch: {
    '$route' (to, from) {
      console.log(from);
      console.log(to);
      const routerDeep = ['/', '/next1'];
      //找到to.path和from.path在routerDeep数组中的下标
      const toDepth = routerDeep.indexOf(to.path);
      const fromDepth = routerDeep.indexOf(from.path);
      this.transitionName = toDepth > fromDepth ? 'fold-left' : 'fold-right';
    }
  }
}
</script>

<style>
.app-view {
  background: yellow;
}
.fold-left-enter-active {
    animation-name: fold-left-in;
    animation-duration: .3s;
  }
  .fold-left-leave-active {
    animation-name: fold-left-out;
    animation-duration: .3s;
  }
  @keyframes fold-left-in {
    0% {
      transform: translate3d(100%, 0, 0);
    }
    /*50% {
      transform: translate3d(50%, 0, 0);
    }*/
    100% {
      transform: translate3d(0, 0, 0);
    }
  }
  @keyframes fold-left-out {
    0% {
      transform: translate3d(0, 0, 0);
    }
    /*50% {
      transform: translate3d(-50%, 0 , 0);
    }*/
    100% {
      transform: translate3d(-100%, 0, 0);
    }
  }

  .fold-right-enter-active {
    animation-name: fold-right-in;
    animation-duration: .3s;
  }
  .fold-right-leave-active {
    animation-name: fold-right-out;
    animation-duration: .3s;
  }
  @keyframes fold-right-in{
    0% {
      width: 100%;
      transform: translate3d(-100%, 0, 0);
    }
    /*50% {
      transform: translate3d(50%, 0, 0);
    }*/
    100% {
      width: 100%;
      transform: translate3d(0, 0, 0);
    }
  }
  @keyframes fold-right-out  {
    0% {
      width: 100%;
      transform: translate3d(0, 0, 0);
    }
    /*50% {
      transform: translate3d(-50%, 0 , 0);
    }*/
    100% {
      width: 100%;
      transform: translate3d(100%, 0, 0);
    }
  }

</style>
