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
        
        <AnnouncementItem
          v-for="a in newAnnouncements" 
          v-bind:key="a.id" 
          v-bind:content ="a"
          :instructorName="instructorName"
          @edit="edit"></AnnouncementItem>
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
  "vue!app/views/controls/AnnouncementsBox",
  "app/views/controls/vueDate"
], function(Vue, _, AnnouncementItem, AnnouncementsBox, vueDate) {
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
        courses: [],
        showNewAnnouncement: false,

        currAnnouncement: this.getNewAnnouncement(),
        instructorName: "",

        newAnnouncements: []        
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

      setAnnouncementStr: function(announcementStr) {
        var announcements = announcementStr.announcements;
        for(var a in announcements) {
          var newAnn = {};
          newAnn.id = announcements[a].announcementID;
          newAnn.content = announcements[a].body;
          newAnn.subject = announcements[a].title;
          newAnn.instructorName = announcements[a].name;
          newAnn.courses = announcements[a].courseString;
          newAnn.availableDate = vueDate.timestampToStr(
              announcements[a].availableDate
          );
          if(announcements[a].expireDate != null) {
            newAnn.expDate = vueDate.timestampToStr(announcements[a].expireDate);
          } else {
            newAnn.expDate = "";
          }
          this.newAnnouncements.push(newAnn);

        }  
            

      },
      setInstructorName: function(name) {
        this.instructorName = name;
      },
      setCourses: function(courses) {
        for(var i=0; i<courses.length; i+=2) {
          this.courses.push({
            id: courses[i],
            name: courses[i+1]
          });
        }
      },

      getNewAnnouncement: function() {
        return {
          title: "New Announcement",
          courses: [],
          subject: "",
          content: "",
          hasExp: false,
          expDate: null,
          expTime: "",
          expHour: "11",  //Default expiration time is 11:59
          expMinute: ":59",   //Default expiration time is 11:59
          availableDate: ""
          
        };
      },

      formatDateStr: function(dateObj) {
        dateObj = new Date(dateObj);
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
          vueDate.zeroPad(2, dateObj.getMinutes().toString()) +
          " " +
          amPm;

        return dateStr;
      }
    }
  };
});
</script>