<template>
  <HeadLine/>
    <div class="container">
        <div class="photocontainer">
            <img :src = this.path alt = this.path/>
            <p id="warn">The copyright and portrait rights have been authorized for personal use and corporate commercial use.</p>
        </div>
    <div class = "text">
        <div id="title1">
            <h2>{{this.title}}</h2>
        </div>
        <a id="author" @click="goAuthorProfile(this.email)">Author: {{this.author}}</a>
        <div id="email"> Email: {{this.email}}</div>
        <div id="photoinfo">
            Tag: {{this.tag}} <br>
            Location: {{this.location}} <br>
            Price: {{this.price}} <br>
        </div> 
        
        <!-- <div id="location1">{{pic.Location}}</div> -->
        
        <button class="downloadPic" @click="download(this.path, title)">Download</button> 
        <br><br>
        <button class="purchasePic" @click="goPurchase(id, path, title, price)">Purchase</button> 
        <br><br>
        <button onclick="javascript:history.back(-1);">Go Back</button>
    </div>
    </div>
    <CommentPage/>
    <MyFooter/>
</template>

<script>
import HeadLine from '@/components/HeadLine.vue'
import MyFooter from '@/components/MyFooter.vue'
import CommentPage from '@/components/CommentPage.vue'
// import axios from 'Axios';
// import {saveAs} from 'file-saver';


export default {
    data(){
        return {
            id: false,
            path: false,
            title: false,
            author: false,
            email: false,
            tag: false,
            location: false,
            price: false
        }
    },

    created() {
        this.id = this.$route.params.id,
        this.path = this.$route.params.photo,
        this.title = this.$route.params.title,
        this.author = this.$route.params.author,
        this.email = this.$route.params.email,
        this.tag = this.$route.params.tag,
        this.location = this.$route.params.location,
        this.price = this.$route.params.price,
        console.log(this.path)
    },

    
    components: {
        HeadLine,
        MyFooter,
        CommentPage,
    },

    methods: {

        download(link, filename) {
            link = link + '?response-content-type=application/octet-stream'
            let a = document.createElement('a')
            a.setAttribute("href", link);
            a.setAttribute("download", filename);
            a.setAttribute("target", "_blank");
            var evObj = document.createEvent('MouseEvents');
            evObj.initMouseEvent('click', true, true, window, 0, 0, 0, 0, 0, false, false, true, false, 0, null);
            a.dispatchEvent(evObj);
        },

        // download(imgsrc, name) { 
        //     fetch(imgsrc).then(async res => await res.blob()).then((blob) => {
        //         const a = document.createElement('a');
        //         a.style.display = 'none';
        //         a.href = URL.createObjectURL(blob);
        //         a.download = name;
        //         document.body.appendChild(a);
        //         a.click();
        //         document.body.removeChild(a);
        //         })  
        // },




        goPurchase(id, image, name, price){
            this.$router.push({name: 'PurchasePicDynamic', params: { id:id, photo: image, title:name, price: price }})
        },

        goAuthorProfile(email){
            console.log("go user profile")
            this.$router.push({name: 'OtherUserProfile', params: { email: email }})
        },
    }
}
</script>

<style scoped>
.container{
    width: 100%;
    height: 700px;
    font-family: Merienda;
    margin-bottom: -220px;;
}
.photocontainer{
    margin-top: 40px;
    margin-right: 20px;
    width: 60%;
    float: right;
}

img{
    width: 80%;
    margin-right: -10px;
}

.text{
    margin-top: 40px;
    width: 40%;
    font-size: 20px;
    font-display: justify;
    color: black;
}

#author{
    margin-left: 0px;
    text-align: left;
    cursor: pointer;
}


#email{
    margin-left: 150px;
    text-align: left;
}

#photoinfo{
    font-size: 18px;
    color:grey;
    margin-top: 10px;
    margin-bottom: 20px;
    text-align: left;
    margin-left: 150px;
}

#warn{
    font-size: 15px;
    color: grey;
}

button{
    text-align:center;
    margin: auto;
    cursor: pointer;
    font-family: Merienda;
    padding: 8px 20px;
    border-radius: 15px;
}
button:hover{
    color: rgb(243, 236, 236);
    background-color: rgb(251, 122, 171);
    box-shadow:  3px 3px grey;
    border-radius: 15px;
}

a:hover{
  border-bottom: 2px solid #fff;;
  color: rgb(251, 122, 171);
}
</style>