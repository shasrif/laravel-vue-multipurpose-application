<template>
    <section class="content">
        <div class="content-header" v-if="$gate.isAdminOrAuthor()">
            <div class="row mb-2">
              <div class="col-sm-6">
                <h1>Users</h1>
              </div>
              <div class="col-sm-6">
                <ol class="breadcrumb float-sm-right">
                  <li class="breadcrumb-item"><router-link to="/dashboard">Home</router-link></li>
                  <li class="breadcrumb-item active">Users</li>
                </ol>
              </div>
            </div>
        </div>

        <div v-if="!$gate.isAdminOrAuthor()">
          <not-found></not-found>
        </div>

        <div class="card" v-if="$gate.isAdminOrAuthor()">
            <div class="card-header">
              <h3 class="card-title">Users</h3>
              <div class="card-tools">
                  <button class="btn btn-primary float-right" @click="openModal">Add New User <i class="fas fa-user-plus"></i></button>
              </div>
            </div>
            <!-- /.card-header -->
            <div class="card-body">
              <table id="example1" class="table table-bordered table-striped">
                <thead>
                <tr>
                  <th class="text-center">ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Phone</th>
                  <th>Type</th>
                  <th>Bio</th>
                  <th class="text-center">Picture</th>
                  <th class="text-center">Status</th>
                  <th class="text-center">Action</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="user in users.data" :key="user.id">
                  <td class="text-center">{{ user.id }}</td>
                  <td>{{ user.name }}</td>
                  <td>{{ user.email }}</td>
                  <td>{{ user.phone }}</td>
                  <td>{{ user.type | textCap }}</td>
                  <!-- <td>{{ user.created_at | myDate }}</td> -->
                  <td>{{ user.bio }}</td>
                  <td class="text-center">{{ user.photo }}</td>
                  <td class="text-center">
                      <a href="" class="btn-sm btn-success"><i class="fas fa-check"></i></a>
                  </td>
                  <td class="text-center">
                      <a href="#" class="btn-sm btn-primary" @click.prevent="updateModalOpen(user)"><i class="fas fa-edit"></i></a>
                      <a href="#" class="btn-sm btn-danger" @click="deleteUser(user.id)"><i class="fas fa-trash"></i></a>
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
            <!-- /.card-body -->
            <div class="card-footer">
                <pagination :data="users" @pagination-change-page="getResults">
                    <span slot="prev-nav"><i class="fas fa-angle-left"></i></span>
                    <span slot="next-nav"><i class="fas fa-angle-right"></i></span>
                </pagination>
            </div>
          </div>
          <!-- /.card -->

        <!-- Modal -->
        <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLongTitle" v-show="!editMode">Add New User</h5>
                        <h5 class="modal-title" id="exampleModalLongTitle" v-show="editMode">Update User</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                        </button>
                    </div>

                    <form @submit.prevent="editMode ? updateUser() : createUser()">
                        <div class="modal-body">
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

                            <div class="form-group" v-show="!editMode">
                                <label>Password</label>
                                <input v-model="form.password" type="password" name="password"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                                <has-error :form="form" field="password"></has-error>
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
                            <div class="custom-file">
                                <input type="file" @change="uploadImage" class="custom-file-input" id="photo">
                                <label class="custom-file-label" for="photo">Upload image...</label>
                            </div>
                        </div>

                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                            <button :disabled="form.busy" type="submit" class="btn btn-primary" v-show="!editMode">Create</button>
                            <button :disabled="form.busy" type="submit" class="btn btn-primary" v-show="editMode">Update</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
    export default {
        data() {
            return {
                users: {},
                editMode: false,
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
            loadUsers() {
              if(this.$gate.isAdminOrAuthor()) {
                axios.get('api/user').then(({ data }) => (this.users = data));
              }
            },

            getResults(page = 1) {
                axios.get('api/user/?page=' + page)
                    .then(response => {
                        this.users = response.data;
                    });
            },

            openModal() {
                this.form.reset();
                $('#addNew').modal('show');
            },

            updateModalOpen(user) {
                this.editMode = true;
                this.form.reset();
                $('#addNew').modal('show');
                this.form.fill(user);
            },

            createUser() {
                this.$Progress.start();
                this.form.post('api/user')
                .then(() => {
                    $('#addNew').modal('hide');
                    Toast.fire({
                      type: 'success',
                      title: 'Created Successfully'
                    })
                    this.$Progress.finish();
                    Fire.$emit('newAction');
                }).catch(() => {

                });
            },

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

            updateUser() {
                this.$Progress.start();
                this.form.put('api/user/' + this.form.id)
                .then(() => {
                    $('#addNew').modal('hide');
                    Toast.fire({
                      type: 'success',
                      title: 'Updated Successfully'
                    })
                    this.$Progress.finish();
                    Fire.$emit('newAction');
                }).catch(() => {

                });
            },

            deleteUser(id) {
                this.$Progress.start();
                Swal.fire({
                  title: 'Are you sure?',
                  text: "You won't be able to revert this!",
                  type: 'warning',
                  showCancelButton: true,
                  confirmButtonColor: '#3085d6',
                  cancelButtonColor: '#d33',
                  confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                    this.form.delete('api/user/' + id)
                    .then(() => {
                        Swal.fire(
                          'Deleted!',
                          'Your file has been deleted.',
                          'success'
                        );
                        Fire.$emit('newAction');
                        this.$Progress.finish();
                    }).catch(() => {

                    });
                })
            }
        },

        created() {
            this.loadUsers();

            Fire.$on('newAction', () => {
                this.loadUsers();
            });

            Fire.$on('searching', () => {
              let query = this.$parent.search;
              axios.get('api/findUser?q=' + query)
              .then((data) => {
                this.users = data.data;
              }).catch(() => {

              })
            });
        }
    }
</script>
