<template>
  <div class="container">
    <div v-show="showMessage" class="err">{{ errMessage }}</div>
    <form @submit.prevent="validateForm">
      <h5>Recipient's phone number</h5>
      <div class="phone">
        <div class="code">
          <label for="phone">Country Code</label>
          <input type="text" v-model="countryCode" maxlength="4"/>
        </div>
        <div class="toNum">
          <label for="phone">Phone Number</label>
        <input
          type="text"
          v-model="phoneNumber"
          placeholder="0123456789"  
        />
        </div>
      </div>
      <div class="message">
        <label for="message">Message</label>
        <textarea
          name="message"
          id="msg"
          cols="30"
          rows="6"
          v-model="message"
        >
        </textarea>
      </div>
      <button type="submit">Send</button>
    </form>
  </div>
</template>

<script>
// const Vonage = require('@vonage/server-sdk')
// const vonage = new Vonage({
//   apiKey: "465084fa",
//   apiSecret: "Dy1qKltrv9peEDUU",
//   signatureSecret: "frhGg8yKBwuLfDxJeWGgbAqe8Qzx07T0yY7CiSThUfiCUveaIQ",
//   signatureMethod: "md5hash",
// })
// import axios from 'axios'
// const baseURL = "https://api.smsmode.com/http/1.6/sendSMS.do"
// const token = "WR7OqpoZeCn2WkkUN6Z1cHdrVznXC7a6"

export default {
  data() {
    return {
      countryCode: "+",
      phoneNumber: null,
      message: "",
      setState: false,
      phoneState: false,
      msgState: false,
      showMessage: false,
      errMessage: "",
    }
  },
  methods: {
    validateForm() {
      if (this.countryCode === "") {
        this.errMessage = 'Fill in the country code'
        setTimeout(() => {
          this.showErr();
        }, 2000);
        this.showMessage = !this.showMessage
        this.setState = false;
      } else if (typeof this.countryCode === 'string') {
        let codeArr = this.countryCode.split('');
        if(codeArr[0] === "+") {
          codeArr.shift();
          let finalVal = Number(codeArr.join(''));
          if(finalVal) {
            this.countryCode = finalVal;
            this.setState = true;
          } else {
            this.errMessage = "Input a valid country code"
            setTimeout(() => {
              this.showErr();
            }, 2000);
            this.showMessage = !this.showMessage
            this.setState = false;
          }
        } else {
          if (codeArr.length === 4) {
            this.errMessage = "Input a valid country code"
            setTimeout(() => {
              this.showErr();
            }, 2000);
            this.showMessage = !this.showMessage
            this.setState = false;
          } else {
            this.countryCode = parseInt(this.countryCode)
            this.setState = true;
          }
        }
      } else {
        false
      }

      let phoneNumber = Number(this.phoneNumber)
      if(phoneNumber) {
        this.phoneNumber = phoneNumber
        this.phoneState = true
      } else {
        this.errMessage = 'Input a valid phone number'
        setTimeout(() => {
          this.showErr();
        }, 2000);
        this.showMessage = !this.showMessage
        this.phoneState = false;
      }

      if (this.message === "") {
        this.errMessage = 'You can not send empty message'
        setTimeout(() => {
          this.showErr();
        }, 2000);
        this.showMessage = !this.showMessage
        this.msgState = false;
      } else if (this.message.length > 140) {
        this.errMessage = 'Maximum text length of 140 characters'
        setTimeout(() => {
          this.showErr();
        }, 2000);
        this.showMessage = !this.showMessage
        this.msgState = false;
      }
      else {
        this.msgState = true;
      }
      if (this.setState && this.phoneState && this.msgState) {
        // Download the helper library from https://www.twilio.com/docs/node/install
        // Your Account Sid and Auth Token from twilio.com/console
        // and set the environment variables. See http://twil.io/secure
        const accountSid = "ACf85322cda19a34df82107aae32fd45e4";
        const authToken = "88933a06600277d5df138eda5c008b2b";
        const client = require('twilio')(accountSid, authToken);

        const to = `+${this.countryCode}${this.phoneNumber}`
        console.log(to);
        client.messages
          .create({
            body: this.message,
            from: '+18184854583',
            to: to
          })
          .then(message => console.log(message.sid));

      } else {
        this.errMessage = 'Fill in all details correctly'
        setTimeout(() => {
          this.showErr();
        }, 2000);
        this.showMessage = !this.showMessage
      }
    },
    showErr() {
      this.showMessage = !this.showMessage
      // ACCOUNT SID = ACf85322cda19a34df82107aae32fd45e4
      // AUTH TOKEN = 88933a06600277d5df138eda5c008b2b
      // PHONE NUMBER = +18184854583
    }
  },
};
</script>

<style>
.container {
  width: 500px;
  margin: 0 auto;
}
form {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.phone {
  display: flex;
  justify-content: center;
  margin-bottom: 15px;
}
input {
  height: 110%;
}
.code input {
  width: 50px;
}
.code label,
.toNum label {
  font-size: 70%;
  margin-bottom: 10px;
  margin-right: 10px;
}
.code,
.toNum {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.message {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-bottom: 15px;
}
button {
  padding: 5px 15px;
}
.err {
  background-color: #DF450B;
  padding: 10px 0;
  color: #FFF;
}
</style>
