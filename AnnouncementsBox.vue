<template>
    <div class="abox">
        <h2>New Announcement</h2>
        <button @click.prevent="$emit('closebox')">Close</button>

        <div id="coursesCheckboxDiv">
            <label class="aboxRequired" for="coursesCheckboxesDiv">Courses</label>
            <div class="inline" v-for="course in courses" id="coursesCheckboxesDiv" :key="course.id">
                
                <input type="checkbox" :id="course.id" :value="course.name" 
                    v-model="checkedCourses">
                <label  :for="course.id">{{ course.name }}</label>               
            </div>       
        </div>

        <div class="subjectDiv">
            <label class="aboxRequired" for="subjectInput" >Subject</label>
            <input type="text" id="subjectInput" @keyup.prevent v-model="aboxSubject">
        </div>  

        <div class="messageDiv">
            <label class="aboxRequired" for="messageTextArea">Message</label>
            <textarea id="messageTextArea" v-model="aboxMessage"></textarea>            
        </div>

        <div class="expireCheckboxDiv">
            <input type="checkbox" v-model="hasExpiration">
            <label for="expireDatePicker" >Expire On:</label>
            <input id="expireDatePicker" @click="hasExpiration=true" type="date" v-model="selDate"> at
            <select id="expirationHourSelect" v-model="selHour">
                <option v-for="hour in hours"
                :value="hour" :key="hour">{{ hour }} </option>
            </select>

            <select id="expirationMinuteSelect" v-model="selMinute">
                <option v-for="minute in minutes"
                :value="minute" :key="minute">{{ minute }} </option>
            </select>

            <select id="expirationAmPm" v-model="selAmPm">
                <option value="am">am</option>
                <option value="pm">pm</option>
            </select>
        </div>    

        <div class="criticalAnnouncementDiv">
            <input type="checkbox" v-model="isCritical">
            <label>Critical Announcement: Email everyone on publish</label>
        </div>

        <div>
            <button class="announcementBoxFooter" @click.prevent="publishAnnouncement()">Publish Announcement</button>
            <button class="announcementBoxFooter" @click.prevent>Save as draft</button>
            <button class="announcementBoxFooter" @click.prevent>Cancel</button>
        </div>
    </div>
</template>

<script>
define([], function() {
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
        selHour: "11",      //Selected hour of expiration
        selMinute: ":59",   //Selected minute of expiration
        title: "New Announcement"   //Title of the announcement box
      }
    },
    mounted: function() {
        $(".aboxRequired").append("<span style='color:red'>*</span>");   
        for(var i=1; i<=12; i++) {
            this.hours.push(zeroPad(2, i.toString()));
        }
        for(var i=0; i<60; i++) {
            this.minutes.push(":" + zeroPad(2, i.toString()));
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
                    validateExpDate(this.selDate, this.selHour, this.selMinute, this.selAmPm);
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

        } 
    }
  };
});

function validateExpDate(userDateStr, selHour, selMin, selAmPm) {
    var dateVars = userDateStr.split("-");
    var userDate = new Date(parseInt(dateVars[0]), parseInt(dateVars[1])-1,
        parseInt(dateVars[2]));
    console.log("date object");
    console.log(userDate);
    var today = new Date();
    var hour = parseInt(selHour);
    var minute = parseInt(selMin.substring(1));     //Take the colon off the beginning of the minute field
    //Turn the hour in 24 hour format
    if(hour == 12) {
        if(selAmPm == "am") {
            hour = 0;
        }
    } else {
        if(selAmPm == "pm") {
            hour += 12;
        }        
    }

    userDate.setHours(hour);
    userDate.setMinutes(minute);
    userDate.setSeconds(0);

    var error = "The expiration date must be after the current instant.";
    var cmp = compareDateDay(userDate, today);
    if(cmp == -1) {     //Selected day before today.
        throw error;
    } else if(cmp == 0) {   //Selected day is today.
        cmp = compareTime(userDate.getHours(), userDate.getMinutes(), userDate.getSeconds(),
            today.getHours(), today.getMinutes(), today.getSeconds());
        if(cmp == -1) {
            throw error;
        }
    }
}

function compareTime(hour1, minute1, second1, hour2, minute2, second2) {
    var cmp = 0;
    if(hour1 < hour2) {
        cmp = -1;
    } else if (hour1 > hour2) {
        cmp = 1;
    } else {
        if(minute1 < minute2) {
            cmp = -1;
        } else if(minute1 > minute2) {
            cmp = 1;
        } else {
            if(second1 < second2) {
                cmp = -1;
            } else if(second1 > second2) {
                cmp = 1;
            }
        }
    }
    return cmp;
}

//Compare two dates to see if they are the same calendar day (ignore the time in Date object)
//Parameters: 2 date objects
//Returns: -1 if date1 is before date2, 0 if they are the same day, and 1 if date1 is after date2
function compareDateDay(date1, date2) {
    var cmp = 0;
    if(date1.getFullYear() < date2.getFullYear()) {
        cmp = -1;
    } else if(date1.getFullYear() > date2.getFullYear()) {
        cmp = 1;
    } else {
        if(date1.getMonth() < date2.getMonth()) {
            cmp = -1;
        } else if (date1.getMonth() > date2.getMonth()) {
            cmp = 1;
        } else {
            if(date1.getDate() < date2.getDate()) {
                cmp = -1;
            } else if(date1.getDate() > date2.getDate()) {
                cmp = 1;
            }
        }
    }
    return cmp;

}

function zeroPad(width, string) {
    var numZeros = width - string.length;
    var zeros = "";
    for(var i=0; i < numZeros; i++) {
        zeros += "0";
    }
    return zeros + string;
}
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
