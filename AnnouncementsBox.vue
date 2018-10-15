<template>
    <div class="abox">
        <h2>{{ content.title }}</h2>
        <button @click.prevent="$emit('closebox')">Close</button>

        <div id="coursesCheckboxDiv">
            <label class="aboxRequired" for="coursesCheckboxesDiv">Courses</label>
            <div class="inline" v-for="course in courses" id="coursesCheckboxesDiv" :key="course.id">
                
                <input type="checkbox" :id="course.id" :value="course.name" 
                    >
                <label  :for="course.id">{{ course.name }}</label>               
            </div>       
        </div>

        <div class="subjectDiv">
            <label class="aboxRequired" for="subjectInput" >Subject</label>
            <input type="text" id="subjectInput" @keyup.prevent v-model="content.subject">
        </div>  

        <div class="messageDiv">
            <label class="aboxRequired" for="messageTextArea">Message</label>
            <textarea id="messageTextArea" v-model="content.content"></textarea>            
        </div>

        <div class="expireCheckboxDiv">
            <input type="checkbox" v-model="content.hasExp">
            <label for="expireDatePicker" >Expire On:</label>
            <input id="expireDatePicker" @click="hasExpiration=true" type="date" > at
            <select id="expirationHourSelect" v-model="content.expHour">
                <option v-for="hour in hours"
                :value="hour" :key="hour">{{ hour }} </option>
            </select>

            <select id="expirationMinuteSelect" v-model="content.expMinute">
                <option v-for="minute in minutes"
                :value="minute" :key="minute">{{ minute }} </option>
            </select>

            <select id="expirationAmPm">
                <option value="am">am</option>
                <option value="pm">pm</option>
            </select>
        </div>    

        <div class="criticalAnnouncementDiv">
            <input type="checkbox" >
            <label>Critical Announcement: Email everyone on publish</label>
        </div>

        <div>
            <button class="announcementBoxFooter" @click.prevent="publishAnnouncement()">Publish Announcement</button>
            <button class="announcementBoxFooter" @click.prevent>Save as draft</button>
            <button class="announcementBoxFooter" @click.prevent="$emit('closebox')">Cancel</button>
        </div>
    </div>
</template>

<script>
define(["app/views/controls/vueDate"], function(vueDate) {
  return {
    props: {
        closebox: Object,
        courses: Array,
        content: Object
    },
    data: function() {
      return {
        aboxMessage: "", //Content of message box
        aboxSubject: "", //Content of subject box
        checkedCourses: [], //Which courses have been selected
        errors: [],
        hasExpiration: false,   //Value of expiration checkbox
        hours: [],      //Initialized to 1-12 for the hour of expiration <select>
        isCritical: false,      //Value of is critical checkbox
        minutes: [],        //Initialized to 1-60 for minute of expiration <select>
        selAmPm: "pm",      //Expiration <select> for am or pm
        selDate: "",        //Selected date of expiration
        selHour: this.setSelHour(),      //Selected hour of expiration
        selMinute: ":59",   //Selected minute of expiration
        title: "New Announcement"   //Title of the announcement box

      }
    },
    mounted: function() {
        $(".aboxRequired").append("<span style='color:red'>*</span>");   
        for(var i=1; i<=12; i++) {
            this.hours.push(vueDate.zeroPad(2, i.toString()));
        }
        for(var i=0; i<60; i++) {
            this.minutes.push(":" + vueDate.zeroPad(2, i.toString()));
        }

    },
    methods: {
        publishAnnouncement: function() {
            //Get the contents of the announcement box
            var newAnnouncement = {
                courses: this.checkedCourses,
                subject: this.aboxSubject,
                message: this.aboxMessage,
                hasExpiration: this.hasExpiration,
                isCritical: this.isCritical
            };

            this.errors = [];
            //Check if everything is filled in and valid
             if(this.checkedCourses == "" || this.checkedCourses == null) {
                this.errors.push("At least one course must be selected.");
            }
            if(this.aboxSubject == "" || this.aboxSubject == null) {
                this.errors.push("Subject must be included.");
            }
            if(this.aboxMessage == "" || this.aboxMessage == null) {
                this.errors.push("A message must be included.");
            }
            
            if(this.hasExpiration) {
                try {   
                    vueDate.validateExpDate(this.selDate, this.selHour, this.selMinute, this.selAmPm);
                } catch (err) {
                    this.errors.push(err);
                }
            }

            if(this.errors.length > 0) {
                var errStr = "";
                for(var i=0; i<this.errors.length; i++) {
                    errStr += this.errors[i] + '\n';
                }
                alert(errStr);
            } else {
                //TODO publish the announcement
            }

        },
        
        setSelHour: function() {
            if(this.content.expDate != null) {
                var selDateStr = vueDate.getDateHour(this.content.expDate).toString();
                return vueDate.zeroPad(2, selDateStr);
            }
            else {
                return "11";
            }
        },

        setSelMinute: function() {
            if(this.content.expDate != null) {
                var selDateStr = vueDate.getDateMinute(this.content.expDate).toString();
                return ":" + vueDate.zeroPad(2, selDateStr);
            } else {
                return ":59";
            }
        }
    }
  };
});

</script>

<style scoped>
body {
    all:unset;
}
label {
    all:unset;
}

.abox {
    padding: 0px;
    border: 2px solid gray;
    height: 300px;
}
.aboxRequired {
    font-weight: 700;
}

.aboxTop {
    color: white;
    font-weight: 700;
    background-color: blue;
}
.announcementBoxFooter {
    float: right;
}

.inline {
    display: inline;
}

</style>
