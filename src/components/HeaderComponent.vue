<template>
  <div class="header-container">
    <div class="left-container">
      <div class="version">
        alpha-version v.0-0-3.5
      </div>
      <div class="logo-container" v-if="this.$root.mainIsOn">
        <img src="../assets/png/logo.png" class="logo">
      </div>
      <div class="logo-container" v-else>
        <img src="../assets/png/logo.png" class="logo hov" @click="goToMain">
      </div>
    </div>
    <div class="middle-container">
      <div class="tabs-container" v-if="!this.$root.myCabIsOn && !this.$root.signUpIsOn && !this.$root.welcomeIsOn">
        <div class="tab waves-effect waves-dark lng"
             v-if="!this.windowChooseLangModalActive && !this.$root.signUpIsOn && !this.$root.myCabIsOn
             && !this.$root.compilerIsOpen && !this.$root.postIsOpen"
             @click="openWindowChooseLang">
          <div v-if="this.$root.activeLang !== null">{{this.$root.activeLang}}</div>
          <div v-else>ВЫБРАТЬ ЯЗЫК</div>
        </div>
        <div class="tab l waves-effect waves-dark" v-bind:class="{active_tab: compilerTabIsActive}" @click="activeTab('compiler', 'tabChange')">
          ОНЛАЙН-КОМПИЛЯТОР
        </div>
        <div class="tab waves-effect waves-dark" v-bind:class="{active_tab: articleTabIsActive}" @click="activeTab('articles', 'tabChange')">
          СТАТЬИ
        </div>
        <div class="tab waves-effect waves-dark" v-bind:class="{active_tab: lessonTabIsActive}" @click="activeTab('lessons', 'tabChange')">
          УРОКИ
        </div>
        <div class="tab waves-effect waves-dark" v-bind:class="{active_tab: taskTabIsActive}" @click="activeTab('tasks', 'tabChange')">
          ЗАДАЧИ
        </div>
        <div class="tab waves-effect waves-dark" v-bind:class="{active_tab: newsTabIsActive}" @click="activeTab('news', 'tabChange')">
          НОВОСТИ
        </div>
      </div>
      <div class="tabs-container" v-if="this.$root.myCabIsOn">
        <div class="tab m waves-effect waves-dark" v-bind:class="{active_tab: myProfileTabIsActive}" @click="activeTab('myProfile', 'tabChange')">
          МОЙ ПРОФИЛЬ
        </div>
        <div class="tab m waves-effect waves-dark" v-bind:class="{active_tab: myPostsTabIsActive}" @click="activeTab('myPosts', 'tabChange')">
          МОИ ПОСТЫ
        </div>
        <div class="tab m waves-effect waves-dark" v-bind:class="{active_tab: mySeensTabIsActive}" @click="activeTab('myViews', 'tabChange')">
          ПРОСМОТРЕНО
        </div>
        <div class="tab m waves-effect waves-dark" v-bind:class="{active_tab: createTabIsActive}" @click="activeTab('creates', 'tabChange')">
          СОЗДАТЬ
        </div>
        <div class="tab m waves-effect waves-dark" v-bind:class="{active_tab: moderTabIsActive}" @click="activeTab('moder', 'tabChange')"
             v-if="this.userRole === '666' || this.userRole ===  '333'">
          МОДЕРАЦИЯ
        </div>
      </div>
    </div>
    <div class="right-container">
      <div class="button-container">

        <div class="btn waves-effect waves-dark cu" v-if="this.$root.authUser && !this.$root.myCabIsOn && !this.$root.signUpIsOn" @click="toMyCabinet">
          Личный кабинет
          <!--<div class="signed-in" v-if="this.$root.authUser">-->
            <!--<div v-if="this.$root.activeUserName !== null && !this.$root.authUser.emailVerified" class="warning">Профиль {{this.$root.activeUserName}}</div>-->
            <!--<div v-else-if="this.$root.activeUserName !== null">Профиль {{this.$root.activeUserName}}</div>-->
          <!--</div>-->
        </div>
        <div v-if="this.$root.authUser && this.$root.myCabIsOn" @click="goToBack" class="cu btn waves-effect waves-dark rl">
          <div class="note-back">
            НА ГЛАВНУЮ
          </div>
          <img src="../assets/png/home.png" class="icon">
        </div>
        <div class="btn waves-effect waves-dark cu logged-in" v-if="this.$root.authUser" @click="logout">
          ВЫЙТИ
        </div>
        <div class="btn waves-effect waves-dark cu logged-out" v-else @click="openLoginWindow">
          ВОЙТИ
        </div>
        <div class="btn waves-effect waves-dark" v-if="!this.$root.authUser && !this.$root.signUpIsOn" @click="toSignUp">
          РЕГИСТРАЦИЯ
        </div>
      </div>
    </div>
    <login :modalActive="modalActive" @close="closeLoginWindow" @logut="logout"></login>
    <chooseLang :windowChooseLangModalActive="windowChooseLangModalActive" @close="closeWindowChooseLang"></chooseLang>
  </div>
</template>

<script>
  import ChooseLangWindow from '../components/ModalWindows/WindowChooseLangComponent'
  import LoginWindow from '../components/ModalWindows/WindowLoginComponent'
  import regeneratorRuntime from "regenerator-runtime";
    export default {
      name: "header-component",
      components: {
        login: LoginWindow,
        chooseLang: ChooseLangWindow
      },
      data () {
        return {
          userRole: '',

          articleTabIsActive: false,
          lessonTabIsActive: false,
          taskTabIsActive: false,
          newsTabIsActive: false,
          compilerTabIsActive: false,
          myProfileTabIsActive: false,
          myPostsTabIsActive: false,
          mySeensTabIsActive: false,
          createTabIsActive: false,
          moderTabIsActive: false,

          loggedIn: "logged-in",
          loggedOut: "logged-out",
          modalActive: false,
          windowChooseLangModalActive: false,
        }
      },

      methods: {

        goToMain(){
          this.$router.push('/')
        },
        goToBack(){
          this.$root.myCabIsOn=false;
          if(sessionStorage.getItem('activeRouteOnMain')){
            this.$router.push({name: sessionStorage.getItem('activeRouteOnMain')})
          }else{
            this.$router.push({name: 'main'})
          }
        },
        activeTab(tab, key) {
          console.log('key '+key);
          // if (this.$root.mainIsOn) {
          if (this.$root.mainIsOn){
            this.articleTabIsActive   = false;
            this.lessonTabIsActive    = false;
            this.taskTabIsActive      = false;
            this.newsTabIsActive      = false;
            this.compilerTabIsActive  = false;
            this.$router.push({name: tab});
            // this.emitCallGetArticles();
            // this.$parent.getArticles();
            // delete sessionStorage['activeTabOnMain']
            sessionStorage.setItem('activeTabOnMain', tab)
          }
          if (!this.$root.mainIsOn && !this.$root.myCabIsOn && !this.$root.signUpIsOn && !this.$root.welcomeIsOn){
            if (tab === 'articles') {
              this.articleTabIsActive   = true;
              this.lessonTabIsActive    = false;
              this.taskTabIsActive      = false;
              this.newsTabIsActive      = false;
              this.compilerTabIsActive  = false;
              if (!this.$root.postIsOpen){
                this.$router.push({name: tab});
                sessionStorage.setItem('activeTabOnMain', tab)
              }else{
                if(key==='tabChange'){
                  this.$router.push({name: tab});
                  sessionStorage.setItem('activeTabOnMain', tab)
                }
              }

            } else if (tab === 'lessons') {
              this.articleTabIsActive   = false;
              this.lessonTabIsActive    = true;
              this.taskTabIsActive      = false;
              this.newsTabIsActive      = false;
              this.compilerTabIsActive  = false;
              if (!this.$root.postIsOpen){
                this.$router.push({name: tab});
                sessionStorage.setItem('activeTabOnMain', tab)
              }else{
                if(key==='tabChange'){
                  this.$router.push({name: tab});
                  sessionStorage.setItem('activeTabOnMain', tab)
                }
              }
            } else if (tab === 'tasks') {
              this.articleTabIsActive   = false;
              this.lessonTabIsActive    = false;
              this.taskTabIsActive      = true;
              this.newsTabIsActive      = false;
              this.compilerTabIsActive  = false;
              if (!this.$root.postIsOpen){
                this.$router.push({name: tab});
                sessionStorage.setItem('activeTabOnMain', tab)
              }else{
                if(key==='tabChange'){
                  this.$router.push({name: tab});
                  sessionStorage.setItem('activeTabOnMain', tab)
                }
              }
            } else if (tab === 'news') {
              this.articleTabIsActive   = false;
              this.lessonTabIsActive    = false;
              this.taskTabIsActive      = false;
              this.newsTabIsActive      = true;
              this.compilerTabIsActive  = false;
              if (!this.$root.postIsOpen){
                this.$router.push({name: tab});
                sessionStorage.setItem('activeTabOnMain', tab)
              }else{
                if(key==='tabChange'){
                  this.$router.push({name: tab});
                  sessionStorage.setItem('activeTabOnMain', tab)
                }
              }
            } else if (tab === 'compiler') {
              this.articleTabIsActive   = false;
              this.lessonTabIsActive    = false;
              this.taskTabIsActive      = false;
              this.newsTabIsActive      = false;
              this.compilerTabIsActive  = true;
              if (!this.$root.postIsOpen){
                this.$router.push({name: tab});
                sessionStorage.setItem('activeTabOnMain', tab)
              }else{
                if(key==='tabChange'){
                  this.$router.push({name: tab});
                  sessionStorage.setItem('activeTabOnMain', tab)
                }
              }
            }
          }else if(this.$root.myCabIsOn){
            if      (tab==='myProfile'){
              this.myProfileTabIsActive    = true;
              this.myPostsTabIsActive      = false;
              this.mySeensTabIsActive      = false;
              this.createTabIsActive       = false;
              this.moderTabIsActive        = false;
              sessionStorage.setItem('activeTabOnMyCab', tab);
              this.$router.push({name: tab});
            }else if(tab==='myPosts'){
              this.myProfileTabIsActive    = false;
              this.myPostsTabIsActive      = true;
              this.mySeensTabIsActive      = false;
              this.createTabIsActive       = false;
              this.moderTabIsActive        = false;
              sessionStorage.setItem('activeTabOnMyCab', tab);
              this.$router.push({name: tab});
            }else if(tab==='myViews'){
              this.myProfileTabIsActive    = false;
              this.myPostsTabIsActive      = false;
              this.mySeensTabIsActive      = true;
              this.createTabIsActive       = false;
              this.moderTabIsActive        = false;
              sessionStorage.setItem('activeTabOnMyCab', tab);
              this.$router.push({name: tab});
            }else if(tab==='creates'){
              this.myProfileTabIsActive    = false;
              this.myPostsTabIsActive      = false;
              this.mySeensTabIsActive      = false;
              this.createTabIsActive       = true;
              this.moderTabIsActive        = false;
              sessionStorage.setItem('activeTabOnMyCab', tab);
              this.$router.push({name: tab});
            }else if(tab==='moder'){
              this.myProfileTabIsActive    = false;
              this.myPostsTabIsActive      = false;
              this.mySeensTabIsActive      = false;
              this.createTabIsActive       = false;
              this.moderTabIsActive        = true;
              sessionStorage.setItem('activeTabOnMyCab', tab);
              this.$router.push({name: tab});
            }

          }
        },

        logout(){
          firebase.auth().signOut();
          this.$root.authUser = null;
          this.$root.activeUserName = null;
          this.$root.activeUserRole = null;
          this.$root.activeUserRate =  null;

          delete localStorage["userName"];
          delete localStorage["userRole"];
          delete localStorage["userRate"];
          delete localStorage["userID"];
          delete localStorage["seenPosts"];
          delete localStorage["seenArticles"];
          delete localStorage["seenNews"];
          delete localStorage["seenLessons"];
          delete localStorage["seenTasks"];
          if(this.$root.myCabIsOn){
            this.$router.push('/')
          }
          // delete localStorage["seenPosts"]
        },

        closeLoginWindow(){
          this.modalActive = false;
        },
        openLoginWindow(){
          this.modalActive = true;
        },

        openWindowChooseLang(){
          this.windowChooseLangModalActive = true;
        },
        closeWindowChooseLang(){
          this.windowChooseLangModalActive = false;
          console.log(this.$root.activeLang)
        },

        toSignUp(){
          this.$router.push('/register')
        },

        toMyCabinet(){
          if (this.$root.authUser === null){
            //TODO сделать модалку кастомную красивую с вызовом модалки логирования
            alert('Please log in in your account')
          }else{
            this.$router.push({name: 'profile'})
          }
        },
        async setUserRole(){
          this.userRole = await this.$root.getRole();
        }
      },

      mounted: function () {
        if (sessionStorage.getItem('activeLang')){
          this.$root.activeLang = sessionStorage.getItem('activeLang');
        }
        // console.log('params '+this.$route.params.postsType)
        this.activeTab(this.$route.params.postsType, 'mounted');
        // console.log(this.$route.params)
      },

      beforeMount: function () {
        if(this.$root.getAuthUser() !== null){
          this.setUserRole();
        }
      },

      updated: function () {
        if(this.$root.getAuthUser() !== null){
          this.setUserRole();
        }
        // console.log('upd head');
        let route = this.$route.name;
        // console.log(route);
        if      (route ==='articles' || route ==='lessons' || route ==='tasks' || route ==='news' || route ==='compiler')     {
          if (sessionStorage.getItem('activeTabOnMain')){
            sessionStorage.setItem('activeRouteOnMain', route);
            this.activeTab(sessionStorage.getItem('activeTabOnMain'), 'updated')
          }
        }else if(sessionStorage.getItem('currentRoute')==='profile'){
          if (sessionStorage.getItem('activeTabOnMyCab')){
            this.activeTab(sessionStorage.getItem('activeTabOnMyCab'), 'updated')
          }

        } else if (route === 'main'){
          this.activeTab(route, 'updated')
        }
      },
    }
</script>

<style lang="scss" scoped>
  .logo, .icon, .logo-container, .rl, .middle-container, .tabs-container, .tab{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
  }

  .logo{
    margin: 0 auto;
    z-index: 10001;
    width: 70%;
    height: 90%;
  }

  .hov:hover{
    transition: 0.2s;
    transform: scale(1.05);
    cursor: pointer;
  }

  .icon{
    /*margin: 0 auto;*/
    max-width: 70%;
    max-height: 70%;
  }

  .logo-container{
    height: 100%;
    width: 250px;
    justify-content: center;
    align-items: center;
    /*background: white;*/
  }

  .header-container{
    display: flex;
    z-index: 100;
    background: rgb(155, 216, 213);
    height: 60px;
    width: 100%;
    position: fixed;
    top: 0;
    left: 0;
    border-radius: 0px 0px 20px 20px;

  }

  .button-container{
    height: 100%;
    width: 300px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .cu{
    /*margin: auto;*/
    margin-right: 20px;
  }
  .cu:hover{
    cursor: pointer;
  }

  .rl{
    /*justify-items: space-between;*/
    align-items: center;
  }

  .note-back{
    @media (max-width: 1200px) {
      display: none;
    }
  }

  .right-container, .left-container{
    width: 20%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .right-container{
    justify-content: flex-start;
  }

  .left-container{
    justify-content: flex-end;
  }

  .middle-container{
    width: 60%;
    height: 100%;
    align-items: center;
    justify-items: center;
  }

  .tabs-container{
    width: 90%;
    justify-content: space-evenly;
    align-items: center;
    height: 100%;
    flex-wrap: nowrap;
  }

  .tab{
    color: white;
    justify-content: center;
    align-items: center;
    width: 100px;
    margin: 0 1px 0 1px;
    height: 80%;
    background: #26a69a;
    border-radius: 5px;
  }

  .tab:hover{
    /*transform: scale(1.1);*/
    transition: .2s all;
    /*z-index: 1000;*/
    background: rgba(183, 180, 180, 0.51);
  }

  .active_tab{
    transform: scale(1.1);
    transition: .2s all;
    z-index: 1000;
    background: rgb(74, 205, 215);
    /*box-shadow: 0 2px 20px 2px rgb(68, 215, 205);*/
  }

  .active_tab:hover{
    background: rgb(74, 205, 215);
    /*box-shadow: 0 2px 20px 2px rgb(68, 215, 205);*/
  }

  .l{
    width: 250px;
  }
  .m{
    width: 190px;
  }

  .tab:hover{
    cursor: pointer;
  }

  .logged-in{
    /*background-color: white;*/
  }

  .logged-in:hover{
    /*box-shadow: 0 2px 20px 2px rgb(215, 85, 75);*/
    background: rgb(215, 116, 96);
  }

  .logged-out{
    /*background-color: white;*/
  }

  .logged-out:hover{
    /*box-shadow: 0 2px 20px 2px rgb(104, 215, 79);*/
    background: rgb(97, 209, 103);
    /*background: #a6ffb7;*/
  }

  .lng{
    font-size: 0.7em;
  }

  .version{
    width: 300px;
    height: 50px;
    justify-content: center;
    align-items: center;
    top: 30px;
    left: -95px;
    display: flex;
    position: fixed;
    color: rgba(0, 0, 0, 0.22);
    background: rgba(171, 137, 25, 0.22);
    transform: rotate(-45deg);
  }
</style>
