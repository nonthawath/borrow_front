<template>

  <v-container>
  
    <v-textarea
            v-model="SubjectName"
              no-resize
              rows="1"
              label="ชื่อวิชา"
              :value="SubjectName"
              v-on:keypress="isText(event)"
              
    ></v-textarea>

    <v-textarea
            v-model="SubjectID"
              no-resize
              rows="1"
              label="รหัสวิชา"
              :value="SubjectID"
              v-on:keypress="isNumber(event)"
    ></v-textarea>
    <v-textarea
            v-model="Sec"
              no-resize
              rows="1"
              label="Sec"
              :value="Sec"
              v-on:keypress="isNumber(event)"
    ></v-textarea>
    <div>
        <v-btn color="#2ECC71" @click="dialog = true">เพิ่มนิสิต</v-btn>
    </div>

     <div>
        <v-btn color="#2ECC71" @click="dialog2 = true" style="margin-top: 10px;" >ดูรายชื่อนิสิต</v-btn>
    </div>

    <div>
        <v-btn color="#2ECC71" @click="submit" style="margin-top: 10px;">สร้างรายวิชา</v-btn>
    </div>
    
    <div class="text-center">
      <v-dialog
        v-model="dialog"
        width="500"
      >
        <v-card>
          <v-card-title class="headline grey lighten-2">
            เพิ่ม Email นิสิตที่เรียน
          </v-card-title>
            <v-textarea
            v-model="StudentEmail"
              no-resize
              rows="1"
              label="Email นิสิต"
              :value="StudentEmail"
            ></v-textarea>
        
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="primary"
              text
              @click="addStudents"
              style="margin-button: 10px;"
            >
              เพิ่ม
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
    <div class="text-center">
      <v-dialog
        v-model="dialog2"
        width="500"
        height="500"
      >
        <v-card>
          <v-card-title class="headline grey lighten-2">
            รายชื่อนิสิต
          </v-card-title>
            <ul id="example-1">
              <li v-for="item in Students" :key="item">
                {{ item }} 
              </li>
            </ul>
        </v-card>
      </v-dialog>
    </div>
  </v-container>
</template>

<script>
  
  import data from '../data/borrow.js'
  export default {
    data() {
      //.substr(0, 10),
        return {
            SubjectName: '',
            SubjectID: '',
            Students: [],
            dialog: false,
            Sec: '',
            dialog2: false,
            StudentEmail: ''
        }
    },
    async created () {
      if(this.$route.query.token) {
       let result = await this.axios.post( 'http://localhost:3000/users/checktoken' , { token : this.$route.query.token })
       //console.log(result.data)
    //    alert(result.data.role)
       if(!result.data.login){
         alert('Not have Token in Session')
         this.$router.push('/')
       }
      }else{
        alert('input your token')
        this.$router.push('/')
      }
    },
    destroyed () {
    },
    methods: {

      //48 - 57 . = 46
      isNumber: function(evt) {
        evt = (evt) ? evt : window.event;
        var charCode = (evt.which) ? evt.which : evt.keyCode;
        if ((charCode > 31 && (charCode < 48 || charCode > 57))) {
          evt.preventDefault();
        } else {
          return true;
        }
      },
      
      // 65 - 122 ascii thai /////dont know why not work >>> if((ch >= 10 && ch <=100) && !(ch <60 && ch >50)
      isText: function(evt) {
        evt = (evt) ? evt : window.event;
        var charCode = (evt.which) ? evt.which : evt.keyCode;
        if (charCode >= 65 && charCode <= 122 && charCode != 91 && charCode != 92 && charCode != 93 && charCode != 94 && charCode != 95 && charCode != 96) {
          return true;
        } else {
          evt.preventDefault();
        }
      },
      async submit(){
        let response = await this.axios.post( 'http://localhost:3000/Subjects/createSubject' , { Professor : data.email , SubjectName : this.SubjectName , SubjectID : this.SubjectID , Students : this.Students , Sec: this.Sec })
        // console.log(re)
        alert(response.data.msg)
      },
      addStudents(){
          this.Students.push( this.StudentEmail )
          this.StudentEmail = ''
      }
    },
  }
</script>