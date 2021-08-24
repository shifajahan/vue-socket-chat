<template>
  <div class="select-username">
    <form @submit.prevent="onSubmit">
      <div class="login">
        <input v-model="username" placeholder="Your name..." />
        <button :disabled="!isValid">Login</button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "SelectUsername",
  data() {
    return {
      username: "",
    };
  },
  computed: {
    isValid() {
      return this.username.length > 2;
    },
  },
  methods: {
    onSubmit() {
      this.$emit("input", this.username);
    },
  },
};
</script>

<style scoped>
.login{
    width: 650px;
    height: 350px;
    border: 3px solid rgb(66, 66, 66);
    border-radius: 6px;
    box-shadow: 4px 5px 6px rgb(140, 144, 151);
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    margin-top:-200px;
}
.login input{
    margin: 10px;
    width: 200px;
    height: 40px;
    border: 1.5px solid rgb(140, 144, 151);
    background-color: transparent;
    padding-left: 5px;
    color: whitesmoke;
    font-size: large;
}
.login input:focus{
    outline: none;
    border: 1.5px solid rgb(188, 189, 190); 
}
.login button{
    width: 200px;
    height: 50px;
    border: none;
    background-color: rgb(57, 112, 168);
    color: rgb(206, 205, 209);
    font-size:large;
    margin-top: 45px;
    font-weight: bold;
}
.login button:hover{
    cursor: pointer;
    background-color: rgb(34, 80, 126);
}
@media only screen and (max-width: 600px) {
    /* For mobile phones: */
    .login{
      width: 100%;
    }
  }
</style>
