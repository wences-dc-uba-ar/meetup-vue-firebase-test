<template>
  <v-container grid-list-xs>
    <v-layout
      row
      wrap>
      <v-flex
        xs12
        sm6
        offset-sm3>
        <h1>Create a new Meetup</h1>
      </v-flex>
    </v-layout>
    <v-layout
      row
      wrap>
      <v-flex xs12 >
        <form @submit.prevent="onCreateMeetup">
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-text-field
                name="title"
                label="Title"
                id="title"
                v-model="title"
                required
              />
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-text-field
                name="location"
                label="Location"
                id="location"
                v-model="location"
                required
              />
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-btn
                color="primary"
                @click="onPickFile">upload</v-btn>
              <input
                type="file"
                style="display: none;"
                ref="fileInput"
                accept="image/*"
                @change="onFilePicked">
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-text-field
                v-show="false"
                name="imageUrl"
                label="image URL"
                id="image-url"
                v-model="imageUrl"
                required
              />
              <img
                :src="imageUrl"
                alt=""
                height="150">
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-textarea
                name="description"
                label="Description"
                id="description"
                v-model="description"
                rows="3"
                required
                outline
              />
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <h4>Choose a Date & Time</h4>
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-date-picker
                v-model="fecha"
                :landscape="true"
                :reactive="true"/>
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-time-picker
                format="24hr"
                landscape
                v-model="hora"/>
            </v-flex>
          </v-layout>
          <v-layout
            row
            wrap>
            <v-flex
              xs12
              sm6
              offset-sm3>
              <v-btn
                color="primary"
                :disabled="!formIsValid"
                type="submit"
              >Create Meetup</v-btn>
            </v-flex>
          </v-layout>
        </form>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      title: "",
      location: "",
      imageUrl: "",
      description: "",
      fecha: "2018-10-20",
      hora: "17:45",
      image: null
    };
  },
  computed: {
    formIsValid() {
      return this.title !== "" && this.location != "" && this.description != "";
    },
    dateUTC() {
      const year = this.fecha.match(/^(\d+)-/)[1];
      const month = this.fecha.match(/-(\d+)-/)[1];
      const day = this.fecha.match(/-(\d+)$/)[1];

      const hour = this.hora.match(/^(\d+)/)[1];
      let minutes = this.hora.match(/:(\d+)/)[1];
      //   minutes = parseInt(minutes) - (new Date()).getTimezoneOffset();

      const dateUTC = new Date(year, month - 1, day, hour, minutes);
      // UTC: console.log("toISOString", dateUTC.toISOString());

      return dateUTC;
    }
  },
  methods: {
    onCreateMeetup() {
      if (!this.formIsValid) {
        return;
      }
      if (!this.image) {
        return;
      }
      const meetupData = {
        title: this.title,
        location: this.location,
        image: this.image,
        description: this.description,
        date: this.dateUTC
      };
      this.$store.dispatch("createMeetup", meetupData);
      this.$router.push("/meetups");
    },
    onPickFile() {
      this.$refs.fileInput.click();
    },
    onFilePicked(event) {
      const files = event.target.files;
      console.log("files", files);
      let filename = files[0].name;
      if (filename.lastIndexOf(".") <= 0) {
        return alert("[@TODO: validacion] no punto en el filename");
      }
      const fileReader = new FileReader();
      fileReader.addEventListener("load", () => {
        this.imageUrl = fileReader.result;
      });
      fileReader.readAsDataURL(files[0]);
      this.image = files[0];
    }
  }
};
</script>
