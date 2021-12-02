<template>
  <div class="home">
    <h1>Regis Locker Test</h1>
    <h1>Name : {{ name }}</h1>
    <h1>Locker UID: {{ lockerUid }}</h1>
    <QRCode :text="lockerUid"></QRCode>
  </div>
</template>

<script>
import VueQRCodeComponent from "vue-qrcode-component";
import io from "socket.io-client";
export default {
  name: "Home",
  components: {
    QRCode: VueQRCodeComponent,
  },
  data() {
    return {
      lockerUid: process.env.VUE_APP_LOCKER_UID,
      socket: null,
      lockerName: null,
    };
  },
  mounted() {
    this.socket = io(
      `${process.env.VUE_APP_SOCKET_URL}:${process.env.VUE_APP_SOCKET_PORT}/${process.env.VUE_APP_SOCKET_NAME_SPACE}`
    );
    this.socket.on("connect", () => {
      console.log("connected");
    });
    this.socket.on("disconnect", () => {
      console.log("disconnected");
    });
    this.socket.on("connect_error", (error) => {
      console.log("connect_error:", error);
    });
    this.socket.on("locker_update", (lockerData) => {
      console.log("locker_update:", lockerData);
      this.lockerName = lockerData.name;
    });
  },
  computed: {
    name() {
      return this.lockerName ?? "Not Already Registered";
    },
  },
};
</script>

<style scoped>
.home {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
</style>
