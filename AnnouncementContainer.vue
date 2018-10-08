<template>
  <div>
    <form>
      <div class="desc">
        <h2>{{ title }}</h2>
      </div>

      <section id="AnnouncementList" style="display:block;">
        <div id="allAnnouncements_newItemHome" class="placeHolder hidden"></div>
        <AnnouncementsBox 
          v-if="showNewAnnouncement" 
          :courses="courses" 
          @closebox="toggleShowNewAnnouncement()"
          :content="currAnnouncement"
          >
        </AnnouncementsBox>
        <button v-if="!showNewAnnouncement" 
          foreditor="#new_Announcement" 
          v-on:click.prevent="toggleShowNewAnnouncement()" 
          id="addNewAnnouncement"
          >
          New Announcement
        </button>
        <ul style="list-style-type:none">
          <li is="AnnouncementItem" 
            v-for="a in announcements" 
            v-bind:key="a.id" 
            v-bind:content="a"
            :instructorName="instructorName"
            @edit="edit"></li>
        </ul>
      </section>
    </form>

    <!--AnnouncementNew :courses="loadedCourses" :closebox="closeBox()"></AnnouncementNew-->
    
  </div>
</template>

<script>
define([
  "Vue",
  "underscore",
  "vue!app/views/controls/AnnouncementItem",
  "vue!app/views/controls/AnnouncementsBox"
], function(Vue, _, AnnouncementItem, AnnouncementsBox) {
  return {
    components: {
      AnnouncementItem,
      AnnouncementsBox
    },
    data: function() {
      return {
        title: "",
        member: 23,
        aBoxContent: {},
        announcements: [],
        courses: [],
        showNewAnnouncement: false,

        currAnnouncement: this.getNewAnnouncement(),
        instructorName: ""
      };
    },
    methods: {
      edit: function(content) {
        content.title = "Edit Announcement";
        this.currAnnouncement = content;
        this.showNewAnnouncement = true;
      },
      toggleShowNewAnnouncement: function() {
        this.showNewAnnouncement = !this.showNewAnnouncement;
        this.currAnnouncement = this.getNewAnnouncement();
      },
      closeBox: function() {},

      setAnnouncements: function(announcements) {

        //this.announcements = announcements;
        for(var i=0; i<announcements.length; i++) {
          var blankAnnouncement = this.getNewAnnouncement();
          blankAnnouncement.subject = announcements[i].title;
          blankAnnouncement.content = announcements[i].text;
          if(announcements[i].expirationDate != null) {
            blankAnnouncement.hasExp = true;
            blankAnnouncement.expDate = announcements[i].expirationDate;
            
          }
          blankAnnouncement.availableDate = this.formatDate(
              new Date(announcements[i].availableDate));
          this.announcements.push(blankAnnouncement);
        }
      },
      setInstructorName: function(name) {
        this.instructorName = name;
        /*for (var i = 0; i < this.announcements.length; i++) {
          var a = this.announcements[i];
          a.instructorName = name;
          var d = new Date(a.availableDate);
          console.log(d.toDateString());
          this.announcements[i].availableDate = this.formatDate(d);

          this.announcements[i] = a;
        }*/
      },
      setCourses: function(courses) {
        for(var i=0; i<courses.length; i+=2) {
          this.courses.push({
            id: courses[i],
            name: courses[i+1]
          });
        }
        console.log(this.courses);
      },

      getNewAnnouncement() {
        return {
          title: "New Announcement",
          courses: [],
          subject: "",
          content: "",
          hasExp: false,
          expDate: "",
          expTime: "",
          availableDate: ""
          
        };
      },

      formatDate: function(dateObj) {
        var dateStr = "";
        var days = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
        var months = [
          "Jan",
          "Feb",
          "Mar",
          "Apr",
          "May",
          "Jun",
          "Jul",
          "Aug",
          "Sep",
          "Oct",
          "Nov",
          "Dec"
        ];
        var hour = dateObj.getHours();
        var amPm = "am";
        if (hour > 12) {
          hour = hour - 12;
          amPm = "pm";
        }
        dateStr +=
          days[dateObj.getDay()] +
          ", " +
          months[dateObj.getMonth()] +
          " " +
          dateObj.getDate() +
          ", " +
          hour +
          ":" +
          dateObj.getMinutes() +
          " " +
          amPm;

        return dateStr;
      }
    }
  };
});
</script>