<template>
  <div>
    <div v-for="(question, index) in myQuestions" :key="index" style="padding: 15px">
      <div class="card w-75" style="margin-left:12%;">
        <div class="card-body">
        <h5 class="card-title" style="text-align:center;">{{new Date(question.createdAt).toDateString()}} | <em>{{question.UserId.first_name + ' ' + question.UserId.last_name}}</em></h5>
        <p class="card-text" style="text-align:center;color:gray;">{{question.title}}</p>
        <p class="card-text" v-html="question.description"></p>
         <h5 style="margin-left:48%;">
          <v-badge>
          <template v-slot:badge>
            <span>{{question.upvotes.length - question.downvotes.length}}</span>
          </template>
          <v-icon
            large
            color="grey lighten-1"
          >
          rate_review 
        </v-icon>
        </v-badge>
        </h5>
        <router-link :to="`/dashboard/view/${question._id}`" style="textDecoration: none;">
        <a href="#" class="btn btn-primary">View</a>
        </router-link>
        <a href="#" class="btn btn-danger ml-3" v-on:click="deleteQuestion(question._id)">Delete</a>
        <EditDialog v-bind:questionDetails="question"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
import EditDialog from "../components/EditDialog";
import axios from '../api/axios'

export default {
  computed: mapState(["myQuestions"]),
  components: {
    EditDialog
  },
  methods: {
    deleteQuestion(QuestionId) {
      this.$swal({
        title: "Are you sure?",
        text: "Once deleted, you will not be able to recover this thread!",
        icon: "warning",
        buttons: true,
        dangerMode: true
      }).then(willDelete => {
        if (willDelete) {
          let token = localStorage.getItem("token");
          axios
            .delete(`/questions/${QuestionId}`, {
              headers: { token }
            })
            .then(({ data }) => {
              this.$swal("Poof! Your thread has been deleted!", {
                icon: "success"
              });
              this.$store.dispatch("getQuestions");
              this.$store.dispatch("getUserQuestions");
              this.$store.dispatch("getUserAnswers");
            })
            .catch(err => {
              console.log(err);
            });
        } else {
          swal("Your imaginary file is safe!");
        }
      });
    }
  }
};
</script>

<style>
</style>
