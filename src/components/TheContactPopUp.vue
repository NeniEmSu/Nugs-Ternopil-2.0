<template>
  <v-dialog max-width="600px" v-model="dialogToggle">
    <v-btn slot="activator" color="success">Contact Us</v-btn>
    <v-card>
      <v-card-title>Drop a Message or Feedback and we will be sure to get back to you shortly.</v-card-title>
      <v-card-text>
        <v-form ref="form" class="px-3">
          <v-text-field
            label="Name"
            :value="title"
            v-model="title"
            prepend-icon="folder"
            :rules="inputRules"
          ></v-text-field>
          <v-textarea
            label="Message"
            :value="info"
            v-model="info"
            prepend-icon="edit"
            :rules="inputRules"
          ></v-textarea>
          <v-menu>
            <v-text-field
              :value="formattedDate"
              slot="activator"
              label="Due Date"
              prepend-icon="date_range"
              :rules="inputRules"
            ></v-text-field>
            <v-date-picker v-model="due"></v-date-picker>
          </v-menu>
          <v-spacer></v-spacer>
          <v-btn class="success mx-0 mt-3" @click="submit" :loading="loading">Add Project</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
import format from "date-fns/format";
import db from "@/fb";

export default {
  data() {
    return {
      title: "",
      info: "",
      due: null,
      loading: false,
      dialogToggle: false,
      inputRules: [v => v.length >= 3 || "Minimum length is 3 characters"]
    };
  },
  methods: {
    submit() {
      if (this.$refs.form.validate()) {
        this.loading = true;
        const project = {
          title: this.title,
          content: this.info,
          due: format(this.due, "Do MMM YYYY"),
          person: "Neni Emsu",
          status: "ongoing"
        };
        db.collection("projects")
          .add(project)
          .then(() => {
            this.loading = false;
            this.title = "";
            this.info = "";
            this.due = null;
            this.dialogToggle = false;
            this.$emit("messageSent");
          });
      }
    }
  },
  computed: {
    formattedDate() {
      return this.due ? format(this.due, "Do MMM YYYY") : "";
    }
  }
};
</script>

<style>
</style>
