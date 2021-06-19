<template>
  <div>
    <new-meeting-form @added="addNewMeeting($event)"></new-meeting-form>

    <h4 v-if="meetings.length == 0">
               Brak zaplanowanych spotkań.
    </h4>
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
                meetings:[]
            };
        },
        methods: {
            addNewMeeting(meeting) {
			    
				this.$http.post('meetings', meeting)
					.then(response => {
						this.$http.get('meetings')
						.then(response => {
						this.meetings=response.data
						})
						.catch(response => {});
                    })
                    .catch(response => {});
            },
            addMeetingParticipant(meeting) {
			
			this.$http.post('meetings/' + meeting.id + '/participants/' + this.username, meeting)
					.then(response => {
						this.$http.get('meetings/'+ meeting.id + '/participants/')
						.then(response => {
						meeting.participants=response.data
						})
                    })
                    .catch(response => {});
                
            },
            removeMeetingParticipant(meeting) {
			
			this.$http.delete('meetings/' + meeting.id + '/participants/' + this.username, meeting)
					.then(response => {
						this.$http.get('meetings/'+ meeting.id + '/participants/')
						.then(response => {
						meeting.participants=response.data
						})
                    })
                    .catch(response => {});  
					

            },
            deleteMeeting(meeting) {
			
			this.$http.delete('meetings/' + meeting.id, meeting)
					.then(response => {
						this.$http.get('meetings')
						.then(response => {
						this.meetings=response.data
						})
                    })
                    .catch(response => {});                
            }		
		},
		 mounted() {

            this.$http.get('meetings')
					.then(response => {
					this.meetings=response.data
                    })
                    .catch(response => {});
        },
	};
</script>
