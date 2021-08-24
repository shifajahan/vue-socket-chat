<template>
    <div v-if="!usernameAlreadySelected" id="app">
      <h1 className="display-5">Let's Chat</h1>
      <select-username
        @input="onUsernameSelection"
      />
    </div>
  <chat v-else />
</template>

<script>
import SelectUsername from "./components/SelectUsername";
import Chat from "./components/Chat";
import socket from "./socket";

export default {
  name: "App",
  components: {
    Chat,
    SelectUsername,
  },
  data() {
    return {
      usernameAlreadySelected: false,
    };
  },
  methods: {
    onUsernameSelection(username) {
      this.usernameAlreadySelected = true;
      socket.auth = { username };
      socket.connect();
    },
    
  },
  created() {
    const sessionID = localStorage.getItem("sessionID");

    if (sessionID) {
      this.usernameAlreadySelected = true;
      socket.auth = { sessionID };
      socket.connect();
    }

    socket.on("session", ({ sessionID, userID }) => {
      // attach the session ID to the next reconnection attempts
      socket.auth = { sessionID };
      // store it in the localStorage
      localStorage.setItem("sessionID", sessionID);
      // save the ID of the user
      socket.userID = userID;
    });

    socket.on("connect_error", (err) => {
      if (err.message === "invalid username") {
        this.usernameAlreadySelected = false;
      }
    });
  },
  destroyed() {
    socket.off("connect_error");
  },
};
</script>

<style>
body{
  padding:0;
  margin: 0;
}


@font-face {
  font-family: Lato;
  src: url("/fonts/Lato-Regular.ttf");
}

#app {
  font-family: Lato, Arial, sans-serif;
  font-size: 14px;
  display: grid;
  place-items: center;
  height: 100vh;
  background-image: url("https://wallpaperaccess.com/full/983279.jpg");
  background-size:cover; 
}
h1{
    color: #ced1f0; 
    font-family: 'Rouge Script', cursive; 
     font-weight: normal; 
     line-height: 48px; 
     margin: 0 0 50px; 
     text-align: center; 
     text-shadow: 0px 2px 2px #8485b3;
     font-size:60px;
  }
</style>
