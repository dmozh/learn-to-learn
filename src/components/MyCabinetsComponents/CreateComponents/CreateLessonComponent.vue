<template>
  <div class="create-components-form-container">
    <div class="header">
      <div class="back-btn waves-effect waves-dark" v-if="isUpdate">
        <img src="../../../assets/png/back_arrow2.png" class="icon" @click="emitReturn">
      </div>
      <div class="optional-container">
        <!--<p>Выберите язык программирования</p>-->
        <label for="sel1">Выберите язык программирования: </label>
        <select id="sel1" class="custom-select">
          <!--тут определяются языки программировпния в селекте-->
          <option v-for="lang in this.$root.langsName" :key="lang.lang_id" :value="lang.lang_name">
            {{lang.lang_name}}
          </option>
        </select>
        <label v-if="isUpdate">Текущий язык: {{reqLangName}}</label>
        <div class="quest-mark-icon tooltip">
          <img src="../../../assets/png/question_mark.png" class="icon qm">
          <span class="tooltiptext">Этот параметр обязателен.</span>
        </div>

      </div>
    </div>

    <div class="main-content-container">
      <form @submit.prevent="sendLesson" class="custom-form">
        <!--Names-->
        <label v-if="isUpdate">Введите название</label>
        <input type="text" v-model="lessonName" placeholder="Введите название урока" class="custom-input-name-field">

        <!--tags-->
        <div class="tags-input-container">
          <input type="text" placeholder="Введите тег" class="tags-input" id="tagInput" v-model="tag">
          <div class="plus-btn-container">
            <div class="plus-btn" @click="addTag">
              <img src="../../../assets/png/plus.png" class="plus-btn-icon"/>
            </div>
          </div>
          <div class="tags-container">
            <label v-if="postTags.length === 0">Добавьте сюда свои тэги</label>
            <span v-for="(tag, index) in postTags"
                 :key="tag.id" class="tag"
                  v-on:click="postTags.splice(index, 1)">{{'#'+tag.name}}&nbsp;
            </span>
          </div>
        </div>

        <!--Contents-->
        <div class="limiter">
          <div class="left-content">
            <label v-if="isUpdate">Введите описание</label>
            <textarea v-model="lessonDescription" placeholder="Введите описание урока" class="desc-textarea"
              v-bind:class="{upd: isUpdate}"
            ></textarea>
            <div class="tasks-container"  v-if="!isUpdate">
              <div class="list-container">
                <div class="list-header">
                  <div class="list-header-id-block">  ID</div>
                  <div class="list-header-name-block">НАЗВАНИЕ</div>
                  <div class="list-header-diff-block">СЛОЖНОСТЬ</div>
                </div>
                <div class="list-items-container">
                  <label v-if="lessonTasks.length===0">Добавьте сюда задачи связанные с этим уроком</label>
                  <div class="list-item list-item-light"
                       v-for="(task, index) in lessonTasks"
                       :key="task.taskId"
                       @click="openTask(task.taskId, task.taskName, task.taskDescription,
                       task.taskText, task.taskDifficulty, task.taskTestInput, task.taskExpectedOutput, index)"
                    >
                    <div class="list-item-id">{{task.taskId}}</div>
                    <div class="list-item-name">{{task.taskName}}</div>
                    <div class="list-item-diff">{{task.taskDifficulty}}</div>
                    <div class="list-item-remove">
                      <!--<div class="rem-btn" v-on:click="remTask(index)">-->
                        <!--<img src="../../../assets/png/plus.png" class="plus-btn-icon"/>-->
                      <!--</div>-->
                    </div>
                  </div>
                </div>
              </div>
              <div class="plus-btn-container">
                <div class="plus-btn" @click="openCreateTaskWindow">
                  <img src="../../../assets/png/plus.png" class="plus-btn-icon"/>
                </div>
              </div>

            </div>
          </div>
          <div class="right-content">
            <label v-if="isUpdate">Введите текст</label>
            <textarea v-model="lessonText" placeholder="Введите текст вашего урока" class="article-text-textarea"></textarea>
            <button class="button waves-effect waves-dark" v-if="!isUpdate">Создать</button>
            <button class="button waves-effect waves-dark" v-else-if="isUpdate">Обновить</button>
          </div>
        </div>
      </form>
    </div>
    <window-create-task-for-lesson :modalActive="modalActive"
                                   :isEdit = "isEdit"
                                   :curTaskId = "curTaskId"
                                   :curTaskName = "curTaskName"
                                   :curTaskDiff = "curTaskDiff"
                                   :curTaskDesc = "curTaskDesc"
                                   :curTaskTxt = "curTaskTxt"
                                   :curTaskInp = "curTaskInp"
                                   :curTaskOut = "curTaskOut"
                                   :index = "index"
                                   @close="closeCreateTaskWindow"></window-create-task-for-lesson>
    <create-error-modal-component
                                  :namePost="emptyName"
                                  :descPost="emptyDesc"
                                  :textPost="emptyText"
                                  :langPost="emptyLang"

                                  :errorModalActive="errorModalActive"
                                  @close="closeError">
    </create-error-modal-component>
  </div>
</template>

<script>
  import WindowCreateTaskForLesson from '../../ModalWindows/WindowCreateTaskForLesson'
  import CreateErrorModalComponent from '../../ModalWindows/CreateErrorModalComponent';
  import regeneratorRuntime from "regenerator-runtime";
  import axios from 'axios'

  export default {
      name: "create-lesson-component",
      components:{
        WindowCreateTaskForLesson,
        CreateErrorModalComponent,
      },
      props: {
        isUpdate: {
          type: Boolean
        },

        reqLessonId:{
          type: Number
        },
        reqLessonName: {
          type: String
        },
        reqLessonDescription: {
          type: String
        },
        reqLessonText: {
          type: String
        },
        reqLessonTags: {
          type: Array
        },
        reqNextLessonTag: {

        },

        reqLessonTasks: {
          type: Array
        },

        reqLangId:{
          type: Number
        },
        reqLangName:{
          type: String
        }
      },
      data(){
        return{
          toastText: '',

          modalActive: false,
          errorModalActive: false,

          emptyName: true,
          emptyDesc: true,
          emptyText: true,
          emptyLang: true,

          isEdit: false,
          curTaskId: null,
          curTaskName: '',
          curTaskDesc: '',
          curTaskDiff: null,
          curTaskTxt: '',
          curTaskInp: '',
          curTaskOut: '',
          index: null,
          //переменные для тэгов
          tag: '',
          nextTag: '',
          postTags: [],

          lessonName: '',
          lessonDescription: '',
          lessonText: '',
          lessonTasks: [],

        }
      },
      methods: {
        openTask(id, name, desc, txt, diff, inp, out, index){
          this.curTaskId=id;
          this.curTaskName=name;
          this.curTaskDesc=desc;
          this.curTaskTxt=txt;
          this.curTaskDiff = diff;
          this.curTaskInp=inp;
          this.curTaskOut=out;
          this.index = index;
          this.isEdit = true;
          this.modalActive = true;
        },
        emitReturn () {
          this.$emit('returns')
        },
        closeCreateTaskWindow(){
          this.isEdit = false;
          this.modalActive = false;
        },
        openCreateTaskWindow(){
          this.isEdit = false;
          this.modalActive = true;
        },

        closeError(){
          this.errorModalActive = false;
          this.emptyName = true;
          this.emptyDesc = true;
          this.emptyText = true;
          this.emptyLang = true;
        },

        async sendLesson(){
          if (this.lessonName.length === 0 || this.lessonDescription.length === 0 || this.lessonText.length === 0){
            if (this.lessonName.length !== 0){
              this.emptyName = false
            }
            if (this.lessonDescription.length !== 0){
              this.emptyDesc = false
            }
            if (this.lessonText.length !== 0){
              this.emptyText = false
            }
            if (document.getElementById("sel1").options.selectedIndex !== 0){
              this.emptyLang = false
            }
            //show modal with error
            this.errorModalActive = true;
          }else{
            //определяем значение параметров lang and userName
            const selectedIndex = document.getElementById("sel1").options.selectedIndex;
            const lang = document.getElementById("sel1").options[selectedIndex].value;
            let body = {};
            let self = this;
            if(!this.isUpdate){
              body = {
                lang: lang,
                authorEmail: this.$root.authUser.email,
                lessonName: this.lessonName,
                lessonDescription: this.lessonDescription,
                lessonText: this.lessonText,
                lessonTags: this.postTags,
                lessonTasks: this.lessonTasks
              };
            }else{
              body = {
                lang: lang,
                // authorEmail: this.$root.authUser.email,
                lessonId: this.reqLessonId,
                lessonName: this.lessonName,
                lessonDescription: this.lessonDescription,
                lessonText: this.lessonText,
                lessonTags: this.postTags,
                // lessonTasks: this.lessonTasks,
              };
            }

            const jBody = JSON.stringify(body);

            if(!this.isUpdate){
              await axios.post(this.$root.URL+'addLesson', jBody).then((response) => {
                // console.log(response);
                let resp = response.data.msg;
                if (resp === true){
                  self.toastText = '<span>Урок был успешно добавлен</span>';
                  M.toast({html: self.toastText, classes: 'rounded success'});
                  self.emitReturn()
                } else if (resp.err_code === '23505') {
                  console.log(resp.err_code);
                  self.toastText = '<span>Урок не был добавлен.&nbsp;</span>' + resp.err_info;
                  M.toast({html: self.toastText, classes: 'rounded warning'});
                }
              }).catch((error) => {
                console.log(error);});
            }else{
              await axios.post(this.$root.URL+'updateUserLesson', jBody).then((response) => {
                // console.log(response);
                let resp = response.data.msg;
                if (resp === true){
                  self.toastText = '<span>Урок был успешно обновлен</span>';
                  M.toast({html: self.toastText, classes: 'rounded success'});
                  self.emitReturn()
                } else if (resp.err_code === '23505') {
                  console.log(resp.err_code);
                  self.toastText = '<span>Урок не был обновлен.&nbsp;</span>' + resp.err_info;
                  M.toast({html: self.toastText, classes: 'rounded'});
                }else if (resp === 'identical'){
                  self.toastText = '<span>Урок идентичен</span>';
                  M.toast({html: self.toastText, classes: 'rounded success'});
                  self.emitReturn()
                }
              }).catch((error) => {
                console.log(error);})
            }
          }
        },

        check(){
          console.log(this.lessonTasks[0])
        },

        addTag(){
          if (this.tag.length === 0){
            alert("Невозможно добавить пустой тэг")
          }else{
            if(this.checkTags()){
              this.postTags.push(
                { id: this.nextTag++,
                  name: this.tag
                });
              this.tag = ''
            }
          }
        },

        checkTags(){
          let check = true;
          for (let curTag in this.postTags){
            if(this.postTags[curTag].name === this.tag){
              check = false;
              break;
            }
          }
          return check;
        },
      },

      mounted: function () {
        if(this.isUpdate){
          this.lessonName = this.reqLessonName;
          this.lessonDescription = this.reqLessonDescription;
          this.lessonText = this.reqLessonText;
          this.postTags = this.reqLessonTags;
          this.nextTag = this.reqNextLessonTag+1;
          this.lessonTasks = this.reqLessonTasks;
          document.getElementById("sel1").value = this.reqLangName;
        }
      }
    }
</script>

<style scoped lang="scss">
  label{
    font-size: large;
    margin: 1vh 2px 0 0;
  }

  .create-components-form-container{
    @media (max-width: 1200px) {
      /*width: 1vw;*/
      /*height: 60vh;*/
    }
    @media (max-width: 900px) {
      /*width: 19vw;*/
      /*height: 50vh;*/
    }
    position: relative;
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    width: 100%;
    /*height: 80vh;*/
    justify-content: center;
    align-items: center;
    /*background: aqua;*/
    /*z-index: 100000;*/
  }

  .icon{
    margin: auto;
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    max-width: 60%;
    max-height: 60%;
    transform: scale(-1, 1);
  }

  .header{
    margin-top: 50px;
    width: 100%;
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start;
  }

  .button{
    width: 10vw;
    /*height: 100%;*/
    height: 4vh;
    background: rgba(22,22,22, 0.1);
    border: none;
    cursor: pointer;
    border-radius: 50px;
    margin: 2.5vh 0 40px 0;
  }

  .button:hover{
    transition: .5s all;
    background: #a6ffb7;
  }

  .custom-input-name-field{
    /*width: 80vw !important;*/
    height: auto !important;
    font-size: 3vw !important;
  }

  /*scrollbar*/
  ::-webkit-scrollbar {
    width: 1em;
  }

  ::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 1px 0 rgba(0,0,0,0.3);
  }

  ::-webkit-scrollbar-thumb {
    background-color: darkgrey;
    outline: 1px solid slategrey;
  }

  .optional-container{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;

    /*width: 100%;*/
    height: 100%;

    justify-content: flex-start;
  }

  .main-content-container{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    /*height: 70vh;*/
    width: 100%;
    justify-content: center;
  }

  .back-btn{
    @media all{
      height: width/2;
    }
    /*margin-left: 50px;*/
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    height: 50px;
    width: 50px;
    transform: rotate(-180deg);
    border-radius: 50%;
    background: #82858c;
    margin-right: 50px;
  }

  .back-btn:hover{
    transform: rotate(-180deg);
    transition: .3s all;
    z-index: 10000000;
    cursor: pointer;
    background: rgba(126, 215, 118, 0.1);
    box-shadow: 0 2px 20px 2px rgb(140, 211, 215);
  }

  .custom-select{
    @media screen and (max-width: 900px){
      /*width: 20vw;*/
      height: 3vh !important;
      font-size: 1.5vh;
    }
    display: -webkit-flex !important;
    -webkit-flex-wrap: wrap !important;
    display: flex !important;
    flex-wrap: wrap !important;
    width: 20vw !important;
    height: 5vh;

    font-size: 2vh;
  }

  .quest-mark-icon{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;

    width: 1vw;
    height: 2vh;
    border-radius: 50%;
  }

  .quest-mark-icon:hover{
    background: rgba(0, 0, 0, 0.31);
    cursor: pointer;
    /*box-shadow: 0 2px 20px 2px rgb(140, 211, 215);*/
  }

  .qm{
    margin: auto;
    transform: scale(1, 1) !important;
  }

  .tooltip .tooltiptext {
    @media screen and (max-width: 1200px){
      width: 20vw;
      height: 7vh;
    }
    visibility: hidden;
    width: 15vw;
    height: 5.4vh;
    background-color: rgba(0, 0, 0, 0.5);
    color: #fff;
    text-align: center;
    border-radius: 2px;
    padding: 5px 0;
    font-size: 10px;
    /* Position the tooltip */
    position: absolute;
    z-index: 1;
  }

  .tooltiptext::after {
    @media screen and (max-width: 1200px){
      left: -20px; bottom: 41px; /* Положение треугольника */
    }
    content: '';
    position: absolute; /* Абсолютное позиционирование */
    left: -20px; bottom: 31px; /* Положение треугольника */
    border: 10px solid transparent; /* Прозрачные границы */
    border-right: 10px solid rgba(0, 0, 0, 0.5); /* Добавляем треугольник */
  }

  .tooltip:hover .tooltiptext {
    transition: all .3s ease;
    opacity: 1;
    transform: translateX(30px);
    visibility: visible;
  }

  .tags-input-container{
    width: 100%;
    height: 5vh;
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start;
  }

  .tags-input{
    width: 25vw !important;
  }

  .plus-btn-container{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    width: 3vw;
    height: 5vh;

    justify-content: center;
    align-items: center;
  }

  .plus-btn, .rem-btn{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;

    width: 1.5vw;
    height: 3vh;
    border-radius: 50%;
  }

  .plus-btn:hover{
    transition: .3s all;
    background: rgba(0, 0, 0, 0.1);
    cursor: pointer;
    /*box-shadow: 0 2px 20px 2px rgb(140, 211, 215);*/
    transform: scale(1.25);
  }

  .plus-btn-icon{
    margin: auto;
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    max-width: 100%;
    max-height: 100%;
  }

  .tags-container{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;

    height: 5vh;
    max-width: 46vw;
    word-break: break-all;
    overflow-y: auto;

    align-items: center;
  }

  .tag{
    height: 1.5vh;
    color: #006dff;
  }

  .tag:hover{
    cursor: pointer;
    transition: .2s;
    transform: scale(1.05);
  }

  .article-text-textarea{
    max-width: 38vw;;
    /*max-height: 45vh;*/
    min-width: 38vw;
    height: 100%;
  }

  .desc-textarea{
    max-width: 25vw;
    /*max-height: 24vh;*/
    min-width: 25vw;
    height: 50%;
    /*min-height: 24vh;*/
    margin-bottom: 1vh;
  }

  .upd{
    height: 92%;
  }

  .limiter{
    max-width: 70vw;
    margin: 15px 0 0 0;

    display: -webkit-flex;
    /*-webkit-flex-wrap: wrap;*/
    display: flex;
    /*flex-wrap: wrap;*/
  }

  .left-content{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;

    width: 25vw;
  }

  .tasks-container{
    display: -webkit-flex;
    /*-webkit-flex-wrap: wrap;*/
    display: flex;
    /*flex-wrap: wrap;*/
    height: 30vh;
    width: 30vw;
    max-width: 30vw;

  }

  .list-container{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    height: 30vh;
    width: 25vw;
    max-width: 25vw;
    overflow-y: auto;
    /*background: aqua;*/
    border: 0.1px solid #c1c4ca;
    /*justify-content: ;*/
  }

  .list-items-container{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    height: 25vh;
    width: 25vw;
    max-width: 25vw;
    overflow-y: auto;

    align-content: baseline;
  }

  .list-header, .list-item{
    display: -webkit-flex;
    /*-webkit-flex-wrap: wrap;*/
    display: flex;
    /*flex-wrap: wrap;*/
    width: 25vw;
    height: 5vh;
    border-bottom: 0.1px solid #c1c4ca;
  }

  .list-item:hover{
    background: #78d7d1;
    transition: .2s;
  }

  .list-item-remove{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    /*border-radius: 50%;*/

  }

  .rem-btn{
    z-index: 1000;
    transform: rotate(45deg);
  }

  .rem-btn:hover{
    background: #e67d7d;
    transform: rotate(45deg);
  }

  .list-item-remove:hover{
    cursor: pointer;
    /*background: #e67d7d;*/
    transform: scale(1.15);
  }

  .list-header-id-block, .list-item-id{
    width: 3vw;
    border-right: 0.1px solid #c1c4ca;
  }
  .list-header-name-block, .list-item-name{
    border-right: 0.1px solid #c1c4ca;
    width: 15vw;
  }
  .list-header-diff-block, .list-item-diff{
    width: 5vw;
    /*border-right: 0.1px solid #c1c4ca;*/
  }

  .list-header-id-block, .list-header-name-block, .list-header-diff-block{
    display: -webkit-flex;
    display: flex;

    justify-content: center;
    align-items: center;

    font-size: 0.8vw;
  }

  .list-item-light{
    background: #cdc6cf7d;
  }

  .list-item-dark{
    background: #63666c;
  }

  .list-item-id{

  }

  .list-item-name{
    word-break: break-all;
    overflow-y: auto;
  }

  .list-item-diff{

  }

  .list-item:hover{
    cursor: pointer;
  }

  .right-content{
    display: -webkit-flex;
    -webkit-flex-wrap: wrap;
    display: flex;
    flex-wrap: wrap;
    width: 45vw;
    margin-left: 50px;
    justify-content: baseline;
  }
</style>
