<template>
    <HeadLine/>
    <!-- <div id = "contianer"> -->
    <div id = "left">
    <h2 id = "select1"><strong>Enter Payment Details </strong></h2>
    <!-- <h3 id = "select2">Select your payment method</h3> -->
    <!-- <button id = 'payment1' onclick="selectFunction1()"> Paypal</button> -->
    <!-- <button id = 'payment2' onclick="selectFunction2()"> Debit Or Credit Card </button> -->
    <!-- <br><br><br> -->

    <div id = "holder">
      <label for="holderName" id = "hn">Cardholder name</label><br>
      <input type="text" id="holderName"><br>
    </div><br>
      
    <div id = "card1">
      <label for="cardNumber" id="cn">Card number</label><br>
      <input type="text" id="cardNumber"><br>
    </div>
      
    <div id = "card2">
      <label for="expirationDate" id="ed">Expiration date</label><br>
      <input type="text" id="expirationDate"><br>
    </div><br><br><br><br>

    <div id = "card3">
      <label for = "securityCode" id="sc">Security code</label><br>
      <input type = "text" id = 'securityCode'><br>
    </div><br><br>

    <div id = "card4">
      <label for = "billingAddress" id="ba">Billing address</label><br>
      <input type = "text" id = 'billingAddress'><br><br>
    </div>
    </div>
    
    <h2 id = "summary">Summary </h2><br><br>
    <h2 id = "price">Price: {{this.price}}</h2><br><br>
 
    <h2 id = "title">{{this.title}}</h2><br><br><br><br>
    <div id = "imagecontainer">
      <img :src = "getImgUrl(path)" id = "image" alt = "getImgUrl(path)"/>   
    </div>

    <button id="checkout" type="button" @click="purchase()">Checkout</button>

</template>

<script>
import HeadLine from '@/components/HeadLine.vue'
import {getFirestore} from "firebase/firestore";
import {doc, getDoc, updateDoc} from "firebase/firestore";
import firebaseApp from '../firebase.js';
import {getAuth, onAuthStateChanged} from "firebase/auth";
import 'firebase/firestore';
const db = getFirestore(firebaseApp);

export default{
  data(){
    return{
      user: false, //
      id: false,
      path: false,
      title: false,
      price: false
    }
  },

  mounted() {
        const auth = getAuth();
        onAuthStateChanged(auth, (user) => {
            if (user) {
                this.user = user;

            }
        })
  },
  
  created() {
        this.id = this.$route.params.id,
        this.path = this.$route.params.photo,
        this.title = this.$route.params.title,
        this.price = this.$route.params.price
    },

    name: "PurchasePic",
    components:{
        HeadLine,

    },

    methods:{
      getImgUrl: function(img) {
            return require('@/assets/' + img);
      },
      async purchase() {
        // var a = document.getElementById("holderName").value
        // var b = document.getElementById("cardNumber").value
        // var c = document.getElementById("expirationDate").value
        // var d = document.getElementById("securityCode").value
        // var e = document.getelementById("billingAddress").value
        try{
          console.log("entering try");
          var photo = {Path: this.path, Title: this.title, Price: this.price}
          const my_res = await getDoc(doc(db, "Transaction", this.user.uid));
          let my_value = my_res.data();
          let my_following = my_value.list;
          if (my_following.includes(photo)) {
                    alert("You already have this transaction!");
          } else {
            my_following.push(photo);
            await updateDoc(doc(db, 'Transaction', this.user.uid), {
              list: my_following
            }),
            alert("Successfully Purchased!")
          }
        }
        catch(error) {
            console.error("Error purchasing this photo: ", error);
            alert("Cannot purchase this photo!");
        }
      }
  }
}

</script>

<style scoped>
#left {
  float: right;
}

#PurchasePic {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: black;
  margin-top: 10px;
  margin-left: 50px;
}
#select1{
  text-align: left;
  margin-bottom: 5px;
}

#select2{
  text-align: left;
  margin-top: 0;
  margin-bottom: 5px;
}

#payment1{
  background-color: white;
  border: 1px black solid;
  color:black;
  font-size: 20px;
  text-align: center;
  width: 200px;
  margin-right: 20px;
  float: left;
}
#payment1:hover{
  background-color: black;
  border: 1px black solid;
  color: white;
  font-size: 20px;
  text-align: center;
  width: 200px;
  margin-right: 20px;
  float: left;
}
#payment2{
  background-color: white;
  border: 1px black solid;
  font-size: 20px;
  text-align: center;
  width: 200px;
  float: left
}
#payment2:hover{
  background-color: black;
  border: 1px black solid;
  color: white;
  font-size: 20px;
  text-align: center;
  width: 200px;
  float: left;
}
#enter{
  text-align: left;
  margin-top: 0;
  margin-bottom: 5px;
}

#holder {
  float:left;
  margin-right: 20px;
}

#hn{
  font-size: 20px;
}

#holderName{
  width: 200px;
  font-size: 20px;
}
#card1{
  float:left;
  margin-right: 20px;
}
#cn{
  font-size: 20px;
}
#cardNumber{
  width: 200px;
  font-size: 20px;
}

#card2{
  float: left;
}
#ed{
  font-size: 20px;
}
#expirationDate{
  width: 200px;
  font-size: 20px;
}

#card3{
  float: left;
}
#sc{
  font-size: 20px;
}
#securityCode{
  width: 200px;
  font-size: 20px;
}

#card4{
  float: left;
}
#ba{
  font-size: 20px;
}
#billingAddress{
  width: 430px;
  font-size: 20px;

}

#left{
  float: left;
  margin-right: 200px;
}

#summary {
  float: left;
}
#price {
  float: left;
  margin-left: -100px;
}
#title{
  float: left;
  margin-left: 0px;
}
#quantity {
  float: left;
  margin-right: 400px;
  margin-top: -10px;
}
#imagecontainer{
  text-align: left;
  margin-top: -30px;
}
img{
  width: 400px;
  height: 280px;
  float: left;
  margin-left: -100px;
}

#checkout{
  background-color: black;
  color:white;
  font-size: 20px;
  text-align: center;
  width: 200px;
  height: 60px;
  margin-top: 20px;
  margin-right: 400px;
  float: left;
}
#checkout:hover{
  background-color: rgb(34, 33, 33);
  color:white;
  font-size: 20px;
  text-align: center;
  width: 200px;
  height: 60px;
  margin-top: 20px;
  margin-right: 390px;
  float: left;
}
</style>
