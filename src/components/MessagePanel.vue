<template>
  <div>
    <div class="header">
      <status-icon :connected="user.connected" />{{ user.username }}
    </div>
    <div class="scrollbar scrollbar-primary">
      <ul class="messages">
        <li
          v-for="(message, index) in user.messages"
          :key="index"
          class="message"
        >
          <div class="messageContainer" v-bind:id="message.fromSelf ? 'you' : 'other'">
            <span class="messageBubble">{{ message.content }}</span>
          </div>
          
        </li>
      </ul>
    </div>

    <form @submit.prevent="onSubmit" class="form">
      <input type="text" v-model="input" placeholder="Your message..." class="input" />
      <button :disabled="!isValid" class="send-button" type="submit">Send</button>
    </form>
  </div>
</template>

<script>
import StatusIcon from "./StatusIcon";

export default {
  name: "MessagePanel",
  components: {
    StatusIcon,
  },
  props: {
    user: Object,
  },
  data() {
    return {
      input: "",
    };
  },
  methods: {
    onSubmit() {
      this.$emit("input", this.input);
      this.input = "";
    }
  },
  computed: {
    isValid() {
      return this.input.length > 0;
    },
  }
};
</script>

<style scoped>
.header {
  line-height: 40px;
  padding: 10px 20px;
  border-bottom: 1px solid #dddddd;
  color: cornsilk;
  font-family:Verdana, Geneva, Tahoma, sans-serif;
  font-size: 25px;
}

.messages {
  margin: 0;
  padding: 20px;
  flex: 80%;
  width: 650px;
  height: 500px;
}

.message {
  list-style: none;
  
}


.form {
  flex: 20%;
  width: 100%;
  display: flex;
  flex-direction: row;
  height:50px;
}

.input {
  
  flex: 90%;
  height: calc(100%-7px);
  border: none;
  border-top: 3px solid rgb(66, 66, 66);
  padding-left: 20px;
  font-size: 20px;
}
input:focus{
    outline: none;
}
.send-button {
 flex: 10%;
  height: calc(100%-7px);
  border: none;
  border-top: 3px solid rgb(66, 66, 66);
  background-color: rgb(57, 112, 168);
  color: rgb(206, 205, 209);
  font-weight: bold;
  font-size: large;
}
.send-button:hover{
    cursor: pointer;
    background-color: rgb(34, 80, 126);
}

.messageContainer{
  display: flex;
  width: calc(100% - 30px);
  max-height:450px;
  flex-direction: row;
  font-weight: bold;
  margin-top: 5px;
    
}
#you{
  justify-content: flex-end;
}
#other{
  justify-content: flex-start;
}
#other .messageBubble{
  background-color: rgb(99, 124, 49);
}
.messageBubble{
  background-color: rgb(59, 42, 207);
  color: rgb(206, 205, 209);
  font-family:Arial, Helvetica, sans-serif;
  padding: 5px;
  border-radius: 10px;
  display: grid;
  place-items: center;
  margin-right: 10px;
  margin-top: 10px;
  opacity: 0.9;
  margin-bottom: 3px;
  font-size: 20px;
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
