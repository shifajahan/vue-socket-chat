<template>
  <div class="message-panel">
    <div class="left-panel scrollbar scrollbar-primary" >
      <user
        v-for="user in users"
        :key="user.userID"
        :user="user"
        :selected="selectedUser === user"
        @select="onSelectUser(user)"
      />
    </div>
    <message-panel
      v-if="selectedUser"
      :user="selectedUser"
      @input="onMessage"
      class="right-panel"
    />
  </div>
</template>

<script>
import socket from "../socket";
import User from "./User";
import MessagePanel from "./MessagePanel";

export default {
  name: "Chat",
  components: { User, MessagePanel },
  data() {
    return {
      selectedUser: null,
      users: []
    };
  },
  methods: {
    onMessage(content) {
      if (this.selectedUser) {
        socket.emit("private message", {
          content,
          to: this.selectedUser.userID,
        });
        this.selectedUser.messages.push({
          content,
          fromSelf: true,
        });
      }
    },
    onSelectUser(user) {
      this.selectedUser = user;
      user.hasNewMessages = false;
    },
  },
  created() {
    socket.on("connect", () => {
      this.users.forEach((user) => {
        if (user.self) {
          user.connected = true;
        }
      });
    });

    socket.on("disconnect", () => {
      this.users.forEach((user) => {
        if (user.self) {
          user.connected = false;
        }
      });
    });

    const initReactiveProperties = (user) => {
      user.hasNewMessages = false;
    };

    socket.on("users", (users) => {
      users.forEach((user) => {
        user.messages.forEach((message) => {
          message.fromSelf = message.from === socket.userID;
        });
        for (let i = 0; i < this.users.length; i++) {
          const existingUser = this.users[i];
          if (existingUser.userID === user.userID) {
            existingUser.connected = user.connected;
            existingUser.messages = user.messages;
            return;
          }
        }
        user.self = user.userID === socket.userID;
        initReactiveProperties(user);
        this.users.push(user);
      });
      // put the current user first, and sort by username
      this.users.sort((a, b) => {
        if (a.self) return -1;
        if (b.self) return 1;
        if (a.username < b.username) return -1;
        return a.username > b.username ? 1 : 0;
      });
    });

    socket.on("user connected", (user) => {
      for (let i = 0; i < this.users.length; i++) {
        const existingUser = this.users[i];
        if (existingUser.userID === user.userID) {
          existingUser.connected = true;
          return;
        }
      }
      initReactiveProperties(user);
      this.users.push(user);
    });

    socket.on("user disconnected", (id) => {
      for (let i = 0; i < this.users.length; i++) {
        const user = this.users[i];
        if (user.userID === id) {
          user.connected = false;
          break;
        }
      }
    });

    socket.on("private message", ({ content, from, to }) => {
      for (let i = 0; i < this.users.length; i++) {
        const user = this.users[i];
        const fromSelf = socket.userID === from;
        if (user.userID === (fromSelf ? to : from)) {
          user.messages.push({
            content,
            fromSelf,
          });
          if (user !== this.selectedUser) {
            user.hasNewMessages = true;
          }
          break;
        }
      }
    });
  },
  destroyed() {
    socket.off("connect");
    socket.off("disconnect");
    socket.off("users");
    socket.off("user connected");
    socket.off("user disconnected");
    socket.off("private message");
  },
};
</script>

<style scoped>
.message-panel{
  background-image: url("https://wallpaperaccess.com/full/983279.jpg");
  background-size:cover; 
  height: 100vh;
  display: flex;
  flex-direction: column;
}
.left-panel {
  position: fixed;
  left: 0;
  top: 0;
  bottom: 0; 
  width: 260px;
  overflow-x: hidden;
  border: 3px solid rgb(66, 66, 66);
  border-radius: 6px;
  box-shadow: 4px 5px 6px rgb(140, 144, 151);
  color: white;
}

.right-panel {
  margin-left: 400px;
  margin-top: 100px;
  width: 650px;
  height: 500px;
  border: 3px solid rgb(66, 66, 66);
  border-radius: 6px;
  box-shadow: 4px 5px 6px rgb(140, 144, 151);
  display: flex;
  flex-direction:column;
}

.scrollbar {
    overflow-y: scroll;
    overflow-x: hidden;
  }
  
  .scrollbar::-webkit-scrollbar {
    width: 6px;
    height: 6px;
  }
  
  .scrollbar::-webkit-scrollbar-thumb {
    border-radius: 5px;
    box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
    background: rgba(0, 0, 0, 0.2);
  }
  
  .scrollbar-primary {
    scrollbar-color: #4285f4 #f5f5f5;
  }
  .scrollbar-primary::-webkit-scrollbar-thumb {
    border-radius: 10px;
    box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
    background-color: #4285F4;
  }
  
</style>
