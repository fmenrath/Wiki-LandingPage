<template>
  <header>
    <div class="header-content">
      <nav>
        <a href="">Zum Wiki</a>
        <a href="">Statistiken</a>
      </nav>
      <div class="user">
        <img src="./assets/user.svg" alt="" id="user-icon">
        <p id="username">Max Mustermann</p>
      </div>
    </div>
  </header>
  <main>
    <div class="content-left">
      <div class="link-block">
        <h2>Neueste Artikel</h2>
        <div class="article" v-for="change in newestArticles" :key="change">
          <a :href="'https://de.wikipedia.org/wiki/'+change.title.replaceAll(' ', '_')" class="article-link">
            <span class="article-name">{{change.title}}</span>
            <span class="article-user">{{change.timestamp.slice(0,10)}}&nbsp;&nbsp;|&nbsp;&nbsp;{{change.user}}</span>
          </a>
        </div>
      </div>
    </div>
    <div class="content-center">
    <img src="./assets/BA_icon.png" alt="ba logo" id="ba-logo">
    <h1>FamKa-Wiki</h1>
    <h3>Wissensportal der Familienkasse</h3>
    <section class="search-wrapper">
      <div class="searchbar">
        <form @submit.prevent="submit()" autocomplete="off">
          <input @keyup="showSuggestions()" type="text" class="searchbar-input" placeholder="Suche" id="input">
          <img src="./assets/search-solid.svg" alt="search" id="search-icon" @click="submit()">
        </form>
        <div class="suggestions" id="suggestions">
          <div class="suggestion" v-for="suggestion in suggestions" :key="suggestion">
            <a :href="'https://de.wikipedia.org/wiki/'+suggestion.replaceAll(' ', '_')" class="suggestion-link">{{suggestion}}</a>
          </div>
        </div>
        <div id="action-buttons-suggestions">
            <a href="https://de.wikipedia.org/">Zum Wiki</a>
            <a href="https://de.wikipedia.org/wiki/Hilfe:Neuen_Artikel_anlegen">Neuer Artikel</a>
          </div>
      </div>
      <div id="action-buttons">
        <a href="https://de.wikipedia.org/">Zum Wiki</a>
        <a href="https://de.wikipedia.org/wiki/Hilfe:Neuen_Artikel_anlegen">Neuer Artikel</a>
      </div>
    </section>
    </div>
    <div class="content-right">
      <div class="link-block">
        <h2>Letzte Änderungen</h2>
        <div class="article" v-for="change in recentEdits" :key="change">
          <a :href="'https://de.wikipedia.org/wiki/'+change.title.replaceAll(' ', '_')" class="article-link">
            <span class="article-name">{{change.title}}</span>
            <span class="article-user">{{change.timestamp.slice(0,10)}}&nbsp;&nbsp;|&nbsp;&nbsp;{{change.user}}</span>
          </a>
        </div>
      </div>
    </div>
  </main>
  <footer>
      © Bundesagentur für Arbeit 2022
  </footer>
</template>

<script>
import './style.css'
import axios from "axios"

export default{
  data(){
    return{
      recentEdits: [],
      newestArticles: [],
      suggestions: []
    }
  },
  async created(){
    const edits = await axios.get('https://de.wikipedia.org/w/api.php?action=query&list=recentchanges&rclimit=5&origin=*&format=json&rctype=edit&rcprop=user|timestamp|title&rcnamespace=0')
    this.recentEdits = edits.data.query.recentchanges

    const newest = await axios.get('https://de.wikipedia.org/w/api.php?action=query&list=recentchanges&rclimit=5&origin=*&format=json&rctype=new&rcprop=user|timestamp|title&rcnamespace=0')
    this.newestArticles = newest.data.query.recentchanges
  },
  methods:{
    async showSuggestions(){
      var buttons = document.getElementById("action-buttons")
      var suggestionButtons = document.getElementById("action-buttons-suggestions")
      var searchterm = document.getElementById("input").value
      if(searchterm !=""){
        const search = await axios.get('https://de.wikipedia.org/w/api.php?action=opensearch&origin=*&limit=5&search='+searchterm)
        buttons.style.display = "none"
        suggestionButtons.style.display = "flex"
        this.suggestions = search.data[1]
      }
      else{
        buttons.style.display = "flex"
        suggestionButtons.style.display = "none"
        this.suggestions = []
      }
    },
    submit(){
      var searchterm = document.getElementById("input").value
      window.open('https://de.wikipedia.org/wiki/'+searchterm);
    }
  }
}
</script>



