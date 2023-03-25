<template>
  <div class="hello">
    <div class="form-wrapper" :class="{ 'slide-left': formSubmitted }">
      <h1>{{ msg }}</h1>
      <form @submit.prevent="getDecision">
        <div class="input-group">
          <label for="input1">Your personal code <br/> (eleven digits)</label>
          <input type="text" id="input1" v-model="input1" pattern="[0-9]{11}" required>
        </div>
        <div class="input-group">
          <label for="input2">Desired credit amount <br/>(2000€ - 10000€)</label>
          <input type="number" id="input2" v-model="input2" min="2000" max="10000" required>
        </div>
        <div class="input-group">
          <label for="input3">Desired installment plan <br/>(months 12-60)</label>
          <input type="number" id="input3" v-model="input3" min="12" max="60" required>
        </div>
        <button type="submit">Submit</button>
      </form>
    </div>
    <div v-if="showResponseWindow">
      <div v-if="loanStatus === 'APPROVED' " class="response-window-accept">
        <h1>{{ this.loanStatus }}</h1>
        <h2>Sum approved: {{ this.sumApproved }}</h2>
        <h2>Period approved: {{ this.suitablePeriod }}</h2>
        <h2>More Info: {{ this.explanation }}</h2>
      </div>
      <div v-if="loanStatus === 'PROPOSITIONED' " class="response-window-propositioned">
        <h1>{{ this.loanStatus }}</h1>
        <h2>Sum approved: {{ this.sumApproved }}</h2>
        <h2>Period approved: {{ this.suitablePeriod }}</h2>
        <h2>More Info: {{ this.explanation }}, but we can not offer to {{ this.userAmount }} on period {{ this.userPeriod }}
          month</h2>
      </div>
      <div v-if="loanStatus === 'REJECTED' " class="response-window-rejected">
        <h1>{{ this.loanStatus }}</h1>
        <h2>More Info: {{ this.explanation }}</h2>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      formSubmitted: false,
      showResponseWindow: false,
      loanStatus: "",
      sumApproved: "",
      suitablePeriod: "",
      explanation: "",
      userAmount: "",
      userPeriod: ""
    };
  },
  methods: {
    getDecision() {
      axios.get("/api/getDecision"
          + "?code=" + this.input1
          + "&amount=" + this.input2
          + "&period=" + this.input3).then(response => {
        this.loanStatus = response.data.loanStatus;
        this.sumApproved = response.data.sumApproved;
        this.suitablePeriod = response.data.suitablePeriod;
        this.explanation = response.data.explanation;
        this.userAmount = this.input2;
        this.userPeriod = this.input3;
        console.log(response.data)
      })
      this.formSubmitted = true;
      setTimeout(() => {
        this.showResponseWindow = true;
      }, 500);

    }
  }
}
</script>
<style scoped>

.response-window-accept {
  position: absolute;
  top: 19.5%;
  right: 20%;
  width: 35%;
  height: 60%;
  background-color: #6de088;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.1);
  color: #ffffff;
}

.response-window-rejected {
  position: absolute;
  top: 19.5%;
  right: 20%;
  width: 35%;
  height: 60%;
  background-color: #ff5733;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.1);
  color: #ffffff;
}

.response-window-propositioned {
  position: absolute;
  top: 19.5%;
  right: 20%;
  width: 35%;
  height: 60%;
  background-color: #ffcc00;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.1);
  color: #ffffff;
}

.form-wrapper {
  width: min-content;
  height: 100%;
  padding: 20px;
  box-sizing: border-box;
  transition: transform 0.5s ease;
}

.slide-left {
  transform: translateX(-80%);
}


.hello {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
}

form {
  display: block;
  flex-direction: column;
  align-items: center;
  background-color: #ffc05c;
  border-radius: 5px;
  padding: 30px;
}

.input-group {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
  margin-bottom: 20px;
}

label {
  font-size: 20px;
  margin-right: 10px;
  white-space: pre-line;
  text-align: left;
  min-width: 200px;
}

input[type="text"] {
  border: none;
  border-radius: 3px;
  padding: 10px;
  flex: 1;
  font-size: 16px;
  background-color: #ffffff;
  box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.1);
  width: 300px;
}

input[type="number"] {
  border: none;
  border-radius: 3px;
  padding: 10px;
  flex: 1;
  font-size: 16px;
  background-color: #ffffff;
  box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.1);
  width: 300px;
}


button[type="submit"] {
  border: none;
  border-radius: 3px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #ff9d47;
  color: #ffffff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button[type="submit"]:hover {
  background-color: #e5616a;
}
</style>