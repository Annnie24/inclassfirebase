<template>
  <div class="about">
    <h1>This is an about page</h1>
    <input type="text" v-model="newMovieTitle" placeholder="Add a new movie"/>
    <button @click="addMovie">Add Movie</button>
    <ul>
      <li v-for="movie in movies" :key="movie">
        id: {{ movie.id }}
        Movie: {{ movie.title }}
        Title: {{ movie.director }}
        <button @click="deleteMovie(movie.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>


<script setup>
/*
setup firebase project
setup vue
npm install firebase + npm install firebase-tools
firebase login (in termninal)
open firebase console and "create a project" +  "create a new database"
change firebase rule (tab) - the date
made placeholder collection (data)

imported firebase config (api key, and more) - Step firebase
imported firebase functions (initializeApp, getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc) - Step firebase
imoported vue functions (ref, onMounted)
created input field for new movie title
created a list of movies and stored it in a ref
created a reference to the movies collection in firebase
created a function to retrieve a new movie to the list
created a function to add a new movie to the list
created a function to delete a movie from the list
install dotenv and create a .env file for security: npm install dotenv
*/

import { ref, onMounted } from 'vue';
import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc } from 'firebase/firestore';

// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";

// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID
};

  // apiKey: "AIzaSyA8bANF3iIpQBz3ypsWxDdrSMVqvA-0rA4",
  // authDomain: "inclassfirebase-a19a5.firebaseapp.com",
  // projectId: "inclassfirebase-a19a5",
  // storageBucket: "inclassfirebase-a19a5.appspot.com",
  // messagingSenderId: "575780740781",
  // appId: "1:575780740781:web:d3789a4110ebe527a5b024"


// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

//step 1: create a ref for the new movie title
const newMovieTitle = ref('');

//step 2: create a list of movies and store it in a ref
const movies = ref([]);

//step 3: create a reference to the movies collection in firebase
const moviesFirebaseCollectionRef = 'movies';

const moviesCollection = collection(db, moviesFirebaseCollectionRef);


//step 3: create a function to retrieve a new movie to the list
onMounted(()=>{
  onSnapshot(moviesCollection,(snapshot)=>{
    movies.value = snapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data() //spread operator
      // title: doc.data().title
      // director: doc.data().director
    }))
  })
})

//step 4: create a function to add a new movie to the list
const addMovie = async () => {
  if (newMovieTitle.value.trim() === '') return; //checks if the input is empty, return stop function

  await addDoc(moviesCollection, {
    title: newMovieTitle.value
  })

  newMovieTitle.value = ''; //clears the input field
}

//step 5: create a function to delete a movie from the list
const deleteMovie = async (id) => {
  await deleteDoc(doc(db, moviesFirebaseCollectionRef, id));

}


</script>


<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
