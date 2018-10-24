<style>
    .widgets-user-header{
        background-position: center center;
        background-size: cover;
        height: 250px;
    }
</style>


<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12 mt-3">
                <!-- Widget: user widget style 1 -->
                <div class="card card-widget widget-user">
                <!-- Add the bg color to the header using any of the bg-* classes -->
                <div class="widget-user-header text-white"
                    style="background-image:url('./img/user-cover.png')">
                    <h3 class="widget-user-username">Elizabeth Pierce</h3>
                    <h5 class="widget-user-desc">Web Designer</h5>
                </div>
                <div class="widget-user-image">
                    <img class="img-circle" :src="getProfilePhoto()" alt="User Avatar">
                </div>
                <div class="card-footer">
                    <div class="row">
                    <div class="col-sm-4 border-right">
                        <div class="description-block">
                        <h5 class="description-header">3,200</h5>
                        <span class="description-text">SALES</span>
                        </div>
                        <!-- /.description-block -->
                    </div>
                    <!-- /.col -->
                    <div class="col-sm-4 border-right">
                        <div class="description-block">
                        <h5 class="description-header">13,000</h5>
                        <span class="description-text">FOLLOWERS</span>
                        </div>
                        <!-- /.description-block -->
                    </div>
                    <!-- /.col -->
                    <div class="col-sm-4">
                        <div class="description-block">
                        <h5 class="description-header">35</h5>
                        <span class="description-text">PRODUCTS</span>
                        </div>
                        <!-- /.description-block -->
                    </div>
                    <!-- /.col -->
                    </div>
                    <!-- /.row -->
                </div>
                </div>
                <!-- /.widget-user -->
            </div>
            <!-- /.col -->
          <div class="col-md-12 mt-3">
            <div class="card">
              <div class="card-header p-2">
                <ul class="nav nav-pills">
                  <li class="nav-item"><a class="nav-link" href="#activity" data-toggle="tab">Activity</a></li>
                  <li class="nav-item"><a class="nav-link active" href="#settings" data-toggle="tab">Settings</a></li>
                </ul>
              </div><!-- /.card-header -->
              <div class="card-body">
                <div class="tab-content">
                  <div class=" tab-pane" id="activity">
                      <h5><center>Display User Activity</center></h5>
                  </div>

                  <div class="active tab-pane" id="settings">
                    <form class="form-horizontal">
                      <div class="form-group">
                        <label for="name" class="col-sm-2 control-label">Name</label>

                        <div class="col-sm-12">
                          <input v-model="form.name" type="text" class="form-control" id="name" name="name" placeholder="Name" :class="{ 'is-invalid': form.errors.has('name') }">
                          <has-error :form="form" field="name"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="email" class="col-sm-2 control-label">Email</label>

                        <div class="col-sm-12">
                          <input v-model="form.email" type="email" class="form-control" id="email" name="email" placeholder="Email" :class="{ 'is-invalid': form.errors.has('email') }">
                          <has-error :form="form" field="email"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="inputExperience" class="col-sm-5 control-label">Experience</label>

                        <div class="col-sm-12">
                          <textarea v-model="form.bio" class="form-control" id="inputExperience" name="inputExperience" placeholder="Experience" :class="{ 'is-invalid': form.errors.has('inputExperience') }"></textarea>
                          <has-error :form="form" field="inputExperience"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="inputProfilePhoto" class="col-sm-8 control-label">Profile Photo</label>

                        <div class="col-sm-12">
                          <input type="file" @change="updateProfile" class="form-control-file" id="inputProfilePhoto" name="inputProfilePhoto" :class="{ 'is-invalid': form.errors.has('inputProfilePhoto') }">
                          <has-error :form="form" field="inputProfilePhoto"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="password" class="col-sm-10 control-label">Password (Leave empty if not changing)</label>

                        <div class="col-sm-12">
                          <input v-model="form.password" type="password" class="form-control" id="password" name="password" placeholder="Password" :class="{ 'is-invalid': form.errors.has('password') }">
                          <has-error :form="form" field="password"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <div class="col-sm-offset-2 col-sm-10">
                          <button @click.prevent="updateInfo" type="submit" class="btn btn-success">Submit</button>
                        </div>
                      </div>
                    </form>
                  </div>
                  <!-- /.tab-pane -->
                </div>
                <!-- /.tab-content -->
              </div><!-- /.card-body -->
            </div>
            <!-- /.nav-tabs-custom -->
          </div>
          <!-- /.col -->
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                form: new Form({
                    id: '',
                    name: '',
                    email: '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''
                })
            }
        },
        mounted() {
            console.log('Component mounted.')
        },
        methods:{
            updateProfile(e){
                //console.log('uploading');
                let file = e.target.files[0];
                let reader = new FileReader();
                if(file['size'] < 2111775) {
                    reader.onloadend = (file) => {
                        //console.log('RESULT', reader.result)
                        this.form.photo = reader.result;
                    }
                    reader.readAsDataURL(file);
                } else {
                    swal({
                        type: 'error',
                        title: 'Oopss...',
                        text: 'You are uploading a large file.'
                    });
                }
            },
            updateInfo(){
                this.$Progress.start();

                this.form.put('api/profile')
                .then(()=>{
                    Fire.$emit('afterTriggered');
                    this.$Progress.finish();
                })
                .catch(()=>{
                    this.$Progress.fail();
                });
            },
            getProfilePhoto(){
                let photo = (this.form.photo.length > 200) ? this.form.photo : "img/profile/"+this.form.photo;
                return photo;
            }
        },
        created() {
            axios.get('api/profile')
            .then(({ data }) => (this.form.fill(data)));
            Fire.$on('afterTriggered', () => {
                axios.get('api/profile')
                .then(({ data }) => (this.form.fill(data)));
            });
        }
    }
</script>
