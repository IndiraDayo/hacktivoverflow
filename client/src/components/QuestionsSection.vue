<template>
    <!-- Page Content -->
    <div class="container">
      <div class="row justify-content-between my-margin-1">
        <div class=" card col-lg-3 my-sticky my-flex">

          <div class="card-body">
            <!-- {{JSON.stringify(user.image}} -->
            <img v-bind:src="user.image" class="cover"  v-if="user.image"/>
            <img v-else-if="!user.image" src="../assets/Original-Facebook-Geek-Profile-Avatar-1.jpg" class="cover"  />
            <p class="my-heading">{{user.name}}</p>
 
              <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#createQuestionModal">
                + Question
              </button>
            <hr>
            <input v-on:keyup.native="getallquestions" v-model="searchval" type="text" class="form-control" placeholder="search question">

            <p class="my-heading">My Questions</p>
            <div class="my-height-2">
              <div v-for="(question, index) in myquestions" v-bind:key="index">
                <button type="button" data-toggle="modal" data-target="#editQuestionModal" v-on:click="showquestion(question._id)"  class="btn small-font">{{question.title}}</button>
              </div>
              
              <!-- <button>Submit Lorem</button>
              <button>Submit Lorem khgjgjg jhgjhgjhgjg gjhgjhgjhg</button>
              <button>Submit Lorem</button>
              <button>Submit Lorem</button>
              <button class="btn">Submit sdgsdgsdgLorem</button>
              <button>Submit Lorem</button>
              <button>Submit Lorem</button>
              <button>Submit Lorem</button>
              <button>Submit Lorem</button> -->
            </div>
            
             
                
          </div>

        </div>
        <!-- /.col-lg-3 -->

        <div class="col-lg-8">
          <div class="row my-height-1 d-flex justify-content-center" >
          
            <div v-for="(question,index) in allquestions" v-bind:key="index" class="card shadow my-margin-3 my-question">
              <!-- <img class="card-img-top cover-blog" v-bind:src="question.image" alt="Card image cap"> -->
              <div class="card-body">
                <h5 class="card-title">{{question.title}}</h5>
                <p>by: {{question.author.name}}</p>
                <button type="button" data-toggle="modal" data-target="#questionModal" v-on:click="showquestion(question._id)">Read Question</button>
              </div>

            </div>

          </div>
        </div>

        <!-- modal create question -->
        <div class="modal fade" id="createQuestionModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog modal-lg" role="document">
            <div class="modal-content">
              
              <div class="modal-body ">
                <p class="my-heading">Create Question</p>
                <hr>
                <form class="form">
                  <p>title:</p>
                  <input v-model="addtitle" type="text" class="form-control" placeholder="your question" required>

                  <p>detail:</p>
                  <textarea v-model="adddescription" class="form-control" id="message-text" placeholder="about your question" required></textarea>
                  <hr>
  
                  <button v-on:click="addquestion" type="submit" class="btn btn-primary" data-dismiss="modal">Submit Question</button>
                </form>
              </div>
            </div>
          </div>
        </div>
        <!-- end create modal -->

        <!-- modal question -->
        <div class="modal fade" id="questionModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog modal-lg" role="document">
            <div class="modal-content">
              
              <div class="modal-body " align="center">
                <p class="my-heading">{{singlequestion.title}}</p>
                <!-- <img class="my-question-image" v-bind:src="singlequestion.image" alt="Card image cap"> -->
                <hr>
                <p class="my-width-1 text-justify">{{singlequestion.description}}</p>
                <hr>
                <p> add comment:</p>
                <input type="text" class="form-control">
                <!-- <hr> -->
                <button type="submit" class="btn btn-primary">Add Comment</button>
                <hr>
                <div>
                  <p>comments:</p>

                </div>
              </div>
            </div>
          </div>
        </div>
        <!-- end modal question -->

        <!-- edit question modal -->
        <div class="modal fade" id="editQuestionModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog modal-lg" role="document">
            <div class="modal-content">
              
              <div class="modal-body ">
                  <p class="my-heading">Edit Question</p>
                  <hr>
                  <form class="form">
                    <p>title:</p>
                    <input v-model="addtitle" type="text" class="form-control" required>

                    <p>about:</p>
                    <textarea v-model="adddescription" class="form-control" id="message-text" required></textarea>
                    <hr>
                    <button v-on:click="editquestion(singlequestion._id)" type="submit" class="btn btn-primary" data-dismiss="modal">Save Question</button>
                  </form>
              </div>
            </div>
          </div>
        </div>
        <!-- end edit question modal -->
        
      </div>

      

    </div>
    <!-- /.container -->
</template>


<script>



export default {
  name: 'QuestionsSection',
  props: ['user'],
  data() {
    return {
      searchval: '',
      singlequestion: {},
      addtitle: '',
      adddescription: '',
      addimage: ''
    }
  },
  methods: {

    getallquestions(val) {
        this.$store.dispatch('getallstoredquestions', val)
    },
    getmyquestions() {
      this.$store.dispatch('getmystoredquestions')
    },
    showquestion(id) {
      axios({
        method: 'GET',
        url: `http://localhost:3000/question/${id}`,
        headers: {
          token: localStorage.getItem('token')
        }
      })
        .then(question => {
          this.singlequestion = question.data.data

          this.addtitle= this.singlequestion.title
          this.adddescription = this.singlequestion.description
          this.addimage= this.singlequestion.image

          console.log('ini satu question ' + JSON.stringify(question));
          
        })
        .catch(err => {
          console.log('ga bisa nge get satu question' + err);
          
        })

    },
    addquestion() {
      let self = this
      // console.log('');
      // console.log(self.addtitle)
      // console.log(self.adddescription);
      // console.log(self.addimage);
      axios({
        method: 'POST',
        url: 'http://localhost:3000/question/add',
        data: {
          title: self.addtitle,
          description: self.adddescription,
          image: self.addimage
        }
      })
        .then( response => {
          console.log('ini di buat question', JSON.stringify(response));
          this.$store.dispatch('getallstoredquestions') //ambil dari actions
          this.$store.dispatch('getmystoredquestions')
        })
        .catch(err => {
          console.log('err di buat question', err);
          
        })
    },
    editquestion(id) {
      let self = this

      axios({
        method: 'PUT',
        url: `http://localhost:3000/question/update/${id}`,
        data: {
          title: self.addtitle,
          description: self.adddescription,
          image: self.addimage
        }
      })
        .then( response => {
          console.log('ini di buat question', JSON.stringify(response));
          this.$store.dispatch('getallstoredquestions') //ambil dari actions
          this.$store.dispatch('getmystoredquestions')
        })
        .catch(err => {
          console.log('err di buat question', err);
          
        })
    }
  },
  created() {
    this.getallquestions()
    this.getmyquestions()
  },
  computed: {
    allquestions() {
      return this.$store.state.allquestions
    },
    myquestions() {
      return this.$store.state.myquestions
    }
  },
  watch: {
        searchval(val) {
          console.log(val)
            if(val !== '') {
              this.getallquestions(val)
            }
        }
    }
}
</script>


<style scoped>
/* .row {
  margin-top: -5%;
  margin-bottom: 5%;
} */
.small-font {
  font-size: 14px;
  
  /* width: max-content; */
}
.card {
  width: 95%;
  border: 0;
}
.form-control {
  width: 95%;
  
}
.my-question-image {
  width: 100%;
  margin-bottom: 50px;
}
.my-width-1 {
  width: 80%;
}

.my-height-1 {
  height: 790px;
  overflow: scroll;
}
.my-height-2 {
  height: 260px;
  overflow: scroll;
}

.my-question {
  height: 150px;
  transition: transform 0.2s;
}

.my-question:hover {
  transform: scale(1.02);
}

.my-heading {
  width: 100%;
  font-size: 25px;
  margin-top: 20px;
  margin-bottom: 20px;
}

.my-margin-1 {
  margin-top: -6%;
}
.my-margin-2 {
  margin-top: 5%;
  margin-bottom: 5%;
}

.my-margin-3 {
  margin-bottom: 5%;
}

.my-flex {
  height: fit-content;
}

.my-sticky {
  position: sticky !important;
}
.cover {
  border-radius: 50%;
  width: 100%;
  height: 215px;
  background-size: cover;
  background-repeat: no-repeat;
  background-position: 50% 50%;
}

.card-img-top {
  width: 100%;
  height: 200px;
}

.cover-blog {
  object-fit: cover;
}

input:focus, textarea:focus {
  background-color: white;
  width: 100%;
  color: #2fad88;
}

textarea {
  margin: auto;
  height: 300px;
}

button {
  line-height: 20px;
  appearance: none;
  outline: 0;
  background: #20c284;
  border: 0;
  padding: 10px 15px;
  color: rgb(255, 255, 255);
  border-radius: 3px;
  width: 100%;
  cursor: pointer;
  font-size: 18px;
  transition-duration: 0.25s;
  margin-bottom: 5px;
}
button:hover {
  background: #00a59d;
}

.btn {
  cursor: pointer;
}
</style>
