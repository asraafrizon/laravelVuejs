  <template>
    <div>
    <form @submit.prevent="createBiodata" type="post" v-show="!editStatus">
    <div class="form-group" :class="{'has-error':errors.name}">
      <input class="form-control" type="text" v-model="biodata.name" placeholder="name">
      <span class="help-block" v-for="error in errors.name" :key="error.key">{{error}}</span>
    </div>
    <div class="form-group" :class="{'has-error':errors.email}">
      <input class="form-control" type="text" v-model="biodata.email" placeholder="email">
      <span class="help-block" v-for="error in errors.email" :key="error.key">{{error}}</span>
    </div>
    <div class="form-group">
      <button type="submit" class="btn btn-info">save</button>
    </div>
    </form>

    <form @submit.prevent="updateBiodata" type="post" v-show="editStatus">
    <div class="form-group" :class="{'has-error':errors.name}">
      <input class="form-control" type="text" v-model="biodataEdit.name" placeholder="name">
      <span class="help-block" v-for="error in errors.name" :key="error.key">{{error}}</span>
    </div>
    <div class="form-group" :class="{'has-error':errors.email}">
      <input class="form-control" type="text" v-model="biodataEdit.email" placeholder="email">
      <span class="help-block" v-for="error in errors.email" :key="error.key">{{error}}</span>
    </div>
    <div class="form-group">
      <button type="submit" class="btn btn-info">update</button>
      <button class="btn btn-success" type="button" @click="cancelEdit()">cancel</button>
    </div>
    </form>

      <table class="table table-hover">
        <thead>
          <tr>
            <th>Name</th>
            <th>Email</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="biodata in biodatas" :key="biodata.key">
            <td>{{biodata.name}}</td>
            <td>{{biodata.email}}</td>
            <td><button class="btn btn-success" @click="getEdit(biodata)">edit</button></td>
            <td><button class="btn btn-danger" @click="getDelete(biodata)">delete</button></td>


          </tr>
        </tbody>
      </table>
    </div>
  </template>

  <script>
    export default {
      data(){
        return{
          biodata:{
            name:'',
            email:''
          },
          biodatas: [],
          errors:[],
          editStatus: false,
          biodataEdit: {
            name:'',
            email:''
          }
        };
      },
      created(){
        this.fetchBiodata();
      },
      methods: {
        createBiodata(){
          axios.post('/biodata',this.biodata).then(respon=>{
            this.biodatas.push(respon.data.biodata);
            this.biodata = {
              name:'',
              email:'',
            };
            this.errors = [];
          }).catch(respon=>{this.errors = respon.response.data.errors;});
        },
        getEdit(data){
          this.editStatus=true;
          this.biodataEdit = data;
        },
        cancelEdit(){
          this.editStatus=false;
          this.biodataEdit = {
              name:'',
              email:''
          }
        },
        updateBiodata(){
          axios.patch('/biodata/'+this.biodataEdit.id,this.biodataEdit).then(respon=>{
            this.editStatus = false;
            this.biodataEdit = {
            name:'',
            email:''
            };
            this.errors = [];
          }).catch(respon=>{this.errors = respon.response.data.errors;});
        },
        fetchBiodata(){
          axios.get('/biodata').then(respon=>{
            this.biodatas = respon.data.data;
          });
        },
        getDelete(data){
          axios.delete('/biodata/'+data.id).then(respon=>{
            let index = this.biodatas.indexOf(data);
            this.biodatas.splice(index,1);
          });
        }
      }
    }
  </script>
