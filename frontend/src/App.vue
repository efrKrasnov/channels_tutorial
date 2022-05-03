<template>
  <chat currentUser="user"></chat>
</template>

<script>
import Chat from './components/Chat.vue'
import WebSocketInstance from './websocket'
export default {
  components: { Chat },
  data()  {
    return {
    }
  },
  created() {
    WebSocketInstance.connect()
  },

}
</script>

<style>

</style>