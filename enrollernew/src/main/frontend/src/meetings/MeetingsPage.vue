<template>
  <div>
    <new-meeting-form @added="addNewMeeting($event)"></new-meeting-form>

    <span v-if="meetings.length == 0">
               Brak zaplanowanych spotkań.
           </span>
    <h3 v-else>
      Zaplanowane zajęcia ({{ meetings.length }})
    </h3>

    <meetings-list :meetings="meetings"
                   :username="username"
                   @attend="addMeetingParticipant($event)"
                   @unattend="removeMeetingParticipant($event)"
                   @delete="deleteMeeting($event)"></meetings-list>
  </div>
</template>

<script>
    import NewMeetingForm from "./NewMeetingForm";
    import MeetingsList from "./MeetingsList";
    export default {
        components: {NewMeetingForm, MeetingsList},
        props: ['username'],
        data() {
            return {
                meetings: [],
                meetingslist: []
            };
        },
     mounted(){
            this.getMeetings();
           
        
        },
        
        methods: {
        	getMeetings(){
        	this.$http.get('meetings')
                     .then(response => {
                         this.meetings = response.data;
                         
                     });
        	
        	},
        	
           addNewMeeting(meeting) {
                this.$http.post('meetings', meeting)
                .then(response => this.meetings.push(response.data));
                    
            },
           
            deleteMeeting(meeting) {
                this.$http.delete('meetings/'+ meeting.id);
                this.meetings.splice(this.meetings.indexOf(meeting), 1);
            },
            addMeetingParticipant(meeting) {
                  
                this.$http.post('meetings/'+ meeting.id +'/participants', {login:this.username})
               
                  .then(response => {meeting.participants.push(response.data)

                  this.getMeetings()})
                    .catch(response => {
                          console.log("zapisanie na zajęcia nie udało się");
                        
                     })
            },
            removeMeetingParticipant(meeting) {
               this.$http.delete('meetings/'+ meeting.id + '/participants/' + this.username)
                 .then(response => {meeting.participants.splice(meeting.participants.indexOf(this.username), 1)
                       this.getMeetings()})
                       .catch(response => {
                          console.log("wypisanie z zajęc nie udało się");
                        
                     })
            }
            
              }}
    
</script>
