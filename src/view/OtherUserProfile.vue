<template>
<HeadLine/>
    <div class="container">
        <button onclick="javascript:history.back(-1);">Go Back</button>
            <div class="profile">
                

                <div class="profile-image" v-if="this.showIcon">
                    <img :src= "url" alt="Preview" id="IconImg"/>
                </div>
                <div v-else class="profile-image">
                    <img src="@/assets/1st_profile.png" id="IconImg"/>
                </div>

                <div class="profile-user-settings">

                    <h1 class="profile-user-name">{{username}}'s Profile</h1>

                    <br>

                </div>

                <div class="profile-stats">

                    <ul>
                        <li><span class="profile-stat-count" @click="goOtherPortfolio(this.email)">{{post}}</span> posts</li>
                        <li><span class="profile-stat-count" @click="goOtherFollower(email)">{{this.num_follower}}</span> followers</li>
                        <li><span class="profile-stat-count" @click="goOtherFollowing(email)">{{this.num_following}}</span> following</li>
                    </ul>
                    <button v-if="email != this.user.email"  class="follow" @click="followUser(email)">Follow</button>
                </div>

                <div class="profile-intro">

                    <p>
                        {{this.intro}}
                    </p>

                </div>

            </div>
            <!-- End of profile section -->
    </div>
    <MyFooter/>

        <!-- End of container -->


</template>

<script>
import HeadLine from '@/components/HeadLine.vue'
import MyFooter from '@/components/MyFooter.vue'
import { useRouter } from "vue-router";
//import { db } from "../firebase.js";
//import { ref } from "firebase/storage";
import {doc, getDoc, updateDoc} from "firebase/firestore";
import firebaseApp from '../firebase.js';
import {getFirestore} from "firebase/firestore";
const db = getFirestore(firebaseApp);
import { getAuth, onAuthStateChanged } from "firebase/auth";
import { ref, getStorage, getDownloadURL} from "firebase/storage"


const auth = getAuth();
export default{
    name: "OtherUserProfile",
    components:{
        HeadLine,
        MyFooter
    },

    data(){
        return{
            user:false,
            email: false,
            username: false,
            posts:false,
            num_follower: false,
            num_following: false,
            intro: false,
            profileiconURL:false,
            url: false,
            showIcon: false
        }
    },
    setup() {
    const router = useRouter();
    const jumpPage = (name) => {
      router.push({
        name,
      });
    };
      return {router,jumpPage}
    },

    created() {
        this.email = this.$route.params.email
        //const otheruser = ref("");
        const load = async () => {
        try {
            const res = await getDoc(doc(db, "Users", this.email));
            let value = res.data();
            console.log(value);
            this.username = value.username;
            this.posts = value.posts;
            this.num_follower = value.followers.length;
            console.log(this.num_follower)
            this.num_following = value.following.length;
            this.intro = value.intro;
            this.profileiconURL = value.profileiconURL;
             console.log("getURL triggered")
            // Get URL for the image inside the storage
            const storage = getStorage();
            const starsRef = ref(storage, 'icons/'+ this.profileiconURL);
            getDownloadURL(starsRef).then((url) => {
                this.url = url
                this.showIcon=true
                })
        } catch (err) {
            alert(err.message);
        }
        };
        load();  

    },

    methods:{
        
        async followUser(email) {
            try{
                const my_res = await getDoc(doc(db, "Users", this.user.email));
                let my_value = my_res.data();
                let my_following = my_value.following;
                if (my_following.includes(email)) {
                    alert("You have already followed this artist!");
                } else {
                    my_following.push(email);
                    await updateDoc(doc(db, 'Users', this.user.email), {
                        following: my_following
                    });

                    const other_res = await getDoc(doc(db, "Users", email));
                    let other_value = other_res.data();
                    let other_followers = other_value.followers;
                    other_followers.push(this.user.email);
                    await updateDoc(doc(db, 'Users', email), {
                        followers: other_followers
                    });
                    alert("Successfully followed!")
                    location.reload();
                }
            } catch(error) {
                alert("Fail to follow!" + error);
            }
            
        },

        goOtherPortfolio(email){
            console.log("go user portfolio")
            this.$router.push({name: 'OtherPortfolio', params: { email: email }})
        },

        goOtherFollower(email){
            console.log("go user follower")
            this.$router.push({name: 'OtherFollower', params: { email: email }})
        },

        goOtherFollowing(email){
            console.log("go user following")
            this.$router.push({name: 'OtherFollowing', params: { email: email }})
        },
    },


    mounted() {
    onAuthStateChanged(auth, (user) => {
      if (user) {
        this.user = user;
        this.userID = this.$route.params.email;
        display(this, this.userID)
        getURL(this);
        //display(user);
      }
    });

    async function getURL(self){
            setTimeout(() => {
            console.log(self.email)
            console.log("getURL triggered")
            // Get URL for the image inside the storage
            const storage = getStorage();
            const starsRef = ref(storage, 'icons/'+ self.email);
            getDownloadURL(starsRef)
            .then((url) => {
            self.url = url
            self.showIcon=true
            })
            }, 500);
        }
    async function display(self){
        let user = await getDoc(doc(db, "Users", self.userID))
        self.username = user.data().username
        self.intro = user.data().intro
        self.post = user.data().posts
        self.following = user.data().following
        self.followers = user.data().followers
        self.email=user.data().email
        console.log(self.profileiconURL)
    }
}
}

</script>

<style lang="less" scoped>

.container {
    margin: 0 auto;
    max-width: 93.5rem;
    background-image: url(https://img.freepik.com/free-vector/hand-drawn-minimal-background_23-2148999828.jpg?w=2000);
    background-size: cover;
    font-family: Merienda;
    color: rgb(20, 19, 20);
    text-align: center;
}

.profile {
    padding: 5rem 0 0 5rem;
    display: flex;
    flex-wrap: wrap;
}
.profile-image {
    float: left;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-left:200px;
    margin-right: 10px;
}

.profile-image img {
    border-radius: 50%;
}

.profile-user-settings,
.profile-stats,
.profile-intro {
    float: left;
    width: calc(66.666% - 2rem);
}

.profile-user-settings {
    margin-top: 1.1rem;
}

.profile-user-name {
    display: inline-block;
    font-size: 3.2rem;
    font-weight: 300;
}

.profile-stats {
    margin-top: 2.3rem;
}

.profile-stats li {
    display: inline-block;
    font-size: 1.6rem;
    line-height: 1.5;
    margin-right: 4rem;
    cursor: pointer;
}

.profile-stats li:last-of-type {
    margin-right: 0;
}

.profile-intro {
    font-size: 1.6rem;
    font-weight: 400;
    line-height: 1.5;
    margin-top: 2.3rem;
}

.profile-real-name,
.profile-stat-count{
    font-weight: 600;
}

    .profile-image img {
        width: 15rem;
    }

    .profile-user-name {
        font-size: 2.2rem;
    }

    .profile-intro {
        font-size: 1.4rem;
        margin-top: 1.5rem;
    }

    .profile-edit-btn,
    .profile-intro,
    .profile-stats {
        flex-basis: 100%;
        float: none;
        width: auto;
        order: 1;
        margin-top: 1.5rem;
    }

    .profile-stats ul {
        display: flex;
        text-align: center;
        padding: 1.2rem 0;
        border-top: 0.1rem solid #dadada;
        border-bottom: 0.1rem solid #dadada;
    }

    .profile-stats li {
        font-size: 1.4rem;
        flex: 1;
        margin: 0;
    }

    .profile-stat-count {
        display: block;
    }
    .profile {
        display: grid;
        grid-template-columns: 1fr 2fr;
        grid-template-rows: repeat(3, auto);
        grid-column-gap: 3rem;
        align-items: center;
    }

    .profile-image {
        grid-row: 1 / -1;
    }

    .profile-image,
    .profile-user-settings,
    .profile-stats,
    .profile-intro{
        width: auto;
        margin: 0;
    }
        .profile {
            grid-template-columns: auto 1fr;
            grid-row-gap: 1.5rem;
        }

        .profile-image {
            grid-row: 1 / 2;
        }

        .profile-user-settings {
            display: grid;
            grid-template-columns: auto 1fr;
            grid-gap: 1rem;
        }

        .profile-edit-btn,
        .profile-stats,
        .profile-intro {
            grid-column: 1 / -1;
        }

        .profile-user-settings,
        .profile-edit-btn,
        .profile-settings-btn,
        .profile-intro,
        .profile-stats {
            margin: 0;
        }

button{
    text-align:center;
    margin: 20px 0 10px 50px;
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

.follow{
    text-align:center;
    margin: 20px 0 10px 10px;
    cursor: pointer;
    font-family: Merienda;
    font-size:15px;
    padding:15px 30px;
    border-radius: 15px;
}
follow:hover{
    color: rgb(243, 236, 236);
    background-color: rgb(251, 122, 171);
    box-shadow:  3px 3px grey;
    border-radius: 15px;
}

</style>