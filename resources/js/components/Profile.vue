<template>
    <section class="content">
        <div class="content-header">
            <div class="row mb-2">
              <div class="col-sm-6">
                <h1>User Profile</h1>
              </div>
              <div class="col-sm-6">
                <ol class="breadcrumb float-sm-right">
                  <li class="breadcrumb-item"><router-link to="/dashboard">Home</router-link></li>
                  <li class="breadcrumb-item active">Profile</li>
                </ol>
              </div>
            </div>
        </div>

        <div class="container-fluid">
            <div class="row">
                <div class="col-md-3">

                    <!-- Profile Image -->
                    <div class="card card-primary card-outline">
                      <div class="card-body box-profile">
                        <div class="text-center">
                          <img class="profile-user-img img-fluid img-circle" src="" alt="User profile picture">
                        </div>

                        <h3 class="profile-username text-center">Nina Mcintire</h3>

                        <p class="text-muted text-center">Software Engineer</p>

                        <ul class="list-group list-group-unbordered mb-3">
                          <li class="list-group-item">
                            <b>Followers</b> <a class="float-right">1,322</a>
                          </li>
                          <li class="list-group-item">
                            <b>Following</b> <a class="float-right">543</a>
                          </li>
                          <li class="list-group-item">
                            <b>Friends</b> <a class="float-right">13,287</a>
                          </li>
                        </ul>

                        <a href="#" class="btn btn-primary btn-block"><b>Follow</b></a>
                      </div>
                      <!-- /.card-body -->
                    </div>
                    <!-- /.card -->

                    <!-- About Me Box -->
                    <div class="card card-primary">
                      <div class="card-header">
                        <h3 class="card-title">About Me</h3>
                      </div>
                      <!-- /.card-header -->
                      <div class="card-body">
                        <strong><i class="fa fa-book mr-1"></i> Education</strong>

                        <p class="text-muted">
                          B.S. in Computer Science from the University of Tennessee at Knoxville
                        </p>

                        <hr>

                        <strong><i class="fa fa-map-marker mr-1"></i> Location</strong>

                        <p class="text-muted">Malibu, California</p>

                        <hr>

                        <strong><i class="fa fa-file-text-o mr-1"></i> Notes</strong>

                        <p class="text-muted">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam fermentum enim neque.</p>
                      </div>
                      <!-- /.card-body -->
                    </div>
                    <!-- /.card -->
                </div>
                <!-- /.col -->

              <div class="col-md-9">
                <div class="card">
                  <div class="card-header p-2">
                    <ul class="nav nav-pills">
                      <li class="nav-item"><a class="nav-link active" href="#settings" data-toggle="tab">Settings</a></li>
                    </ul>
                  </div><!-- /.card-header -->
                  <div class="card-body">
                    <div class="tab-content">
                      <div class="tab-pane active" id="settings">
                        <form>
                            <div class="form-group">
                                <label>Name</label>
                                <input v-model="form.name" type="text" name="name"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                                <has-error :form="form" field="name"></has-error>
                            </div>

                            <div class="form-group">
                                <label>Email</label>
                                <input v-model="form.email" type="email" name="email"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                                <has-error :form="form" field="email"></has-error>
                            </div>

                            <div class="form-group">
                                <label>Phone</label>
                                <input v-model="form.phone" type="text" name="phone"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('phone') }">
                                <has-error :form="form" field="phone"></has-error>
                            </div>

                            <div class="form-group">
                                <label for="type">User Type</label>
                                <select id="type" name="type" v-model="form.type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option value="" selected>Choose user type</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">User</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label>Bio</label>
                                <textarea v-model="form.bio" name="bio"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                                <has-error :form="form" field="bio"></has-error>
                            </div>
                            <div class="form-group">
                                <div class="custom-file">
                                    <input type="file" @change="uploadImage" class="custom-file-input" id="photo">
                                    <label class="custom-file-label" for="photo">Upload image...</label>
                                </div>
                            </div>
                            <div class="form-group">
                                <button type="submit" @click.prevent="updateProfile" class="btn btn-primary">Update</button>
                            </div>
                        </form>
                      </div>
                      <!-- /.tab-pane -->
                    </div>
                    <!-- /.tab-content -->
                  </div><!-- /.card-body -->
                </div>
              </div>
              <!-- /.col -->
            </div>
            <!-- /.row -->
        </div>
    </section>
</template>

<script>
    export default {
        data() {
            return {
                form: new Form({
                    id: '',
                    name: '',
                    email: '',
                    phone:  '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''
                })
            }
        },

        methods: {
            uploadImage(e) {
                let file = e.target.files[0];
                //console.log(file);
                let reader = new FileReader();
                if(file['size'] < 1048576) {
                    reader.onloadend = (file) => {
                        this.form.photo = reader.result;
                    }
                    reader.readAsDataURL(file);
                } else {
                    Swal.fire(
                      'Ops!',
                      'Your file is large.',
                      'worning'
                    );
                }
            },

            updateProfile() {
                this.$Progress.start();
                this.form.put('api/profile')
                .then(() => {
                    Toast.fire({
                      type: 'success',
                      title: 'Updated Successfully'
                    })
                    this.$Progress.finish();
                }).catch(() => {

                });
            }
        },

        mounted() {
            console.log('Component mounted.')
        },

        created() {
            axios.get('api/profile').then(({ data }) => (this.form.fill(data)));
        }
    }
</script>