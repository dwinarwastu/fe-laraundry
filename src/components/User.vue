<template>
    <div>
        <main>
            <div class="container-fluid px-4">
                <h1 class="mt-4">Data User</h1>
                <div class="card mb-4">
                    <div class="card-body">
                        <a class="btn btn-success" v-b-modal.modal_user @click="Add()">Tambah User</a>
                        <table class="table mt-2">
                            <tr class="table-dark">
                                <td>ID</td>
                                <td>NAMA</td>
                                <td>USERNAME</td>
                                <td>ROLE</td>
                                <td>NAMA OUTLET</td>
                                <td>AKSI</td>
                            </tr>
                            <tr v-for="(use, index) in user" :key="index">
                                <td>{{ index + 1 }}</td>
                                <td>{{ use.name }}</td>
                                <td>{{ use.username }}</td>
                                <td>{{ use.role }}</td>
                                <td>{{ use.outlet.name_outlet }}</td>
                                <td>
                                    <a v-b-modal.modal_user href="#" class="btn btn-info" @click="Edit(use)">Ubah</a>
                                    <a href="#" class="btn btn-danger" @click="Delete(use.id)">Hapus</a>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
            </div>
        </main>

        <b-modal 
            id="modal_user" 
            ref="modal" 
            title="Form User" 
            size="md" 
            @ok="Save">
            <form>
                <div class="form-floating mb-3 mb-md-0">
                    <input v-model="name" placeholder="Enter your first name" v-modal="name" id="inputname" class="form-control" type="text"/>
                    <label for="inputname">Nama User</label>
                </div>
                <div class="form-floating mb-3 mb-md-0">
                    <input v-model="username" placeholder="Enter your first name" v-modal="username" id="username" class="form-control" type="text"/>
                    <label for="username">Username</label>
                </div>
                <div class="form-floating mb-3 mb-md-0">
                    <input v-model="password" placeholder="Enter your first name" v-modal="username" id="password" class="form-control" type="password"/>
                    <label for="password">Password</label>
                </div>
                <div class="form-floating mb-3 mb-md-0">
                    <select v-model="role" class="form-control">
                        <option value="admin">Admin</option>
                        <option value="kasir">Kasir</option>
                        <option value="owner">Owner</option>
                    </select>

                    <label for="inputJk">Role</label>
                </div>
                <div class="form-floating mb-3 mb-md-0">
                    <b-form-select class="form-control" v-model="id_outlet" :options="data_outlet">

                    </b-form-select>
                    <label for="id_outlet" class="col-form-label">Outlet</label>
                </div>
            </form>
        </b-modal>
    </div>
</template>
<script>
module.exports =  {
    data: function(){
        return {
            name: "",
            id_outlet: "",
            username: "",
            password:"",
            role:"",
            user: [],
            data_outlet: [],
            action:""
        }
    },
    methods: {
        getData: function(){
            let config = {
                headers : {
                "Authorization" : "Bearer " + this.$cookies.get('Authorization')
                }
            }

          axios.get(base_url + '/user', config)
          .then( response => {
              console.log(response);
            if(response.data.success == true){
                this.user = response.data.data.user;
            }
          })

        },

        OutletDropdown: function() {
            let config = {
                headers: {
                    Authorization: "Bearer " + this.$cookies.get("Authorization"),
                },
            };
            axios.get(base_url + "/outlet", config).then((response) => {
                let json_outlet = response.data.data.outlet;
                let list_outlet = [
                    {
                        value: "",
                        text : "--Pilih Outlet--", 
                    },
                ];
                json_outlet.forEach((element) => {
                    list_outlet.push({
                        value: element.id_outlet,
                        text : element.name_outlet,
                    });
                });
                this.data_outlet = list_outlet;
            });
        },

        Add: function(){
            this.action = "insert";
            this.id = "";
            this.name = "";
            this.username = "";
            this.password = "";
            this.role = "";
            this.id_outlet = "";
        },
        Edit: function(item){
            this.action = "update";
            this.id = item.id;
            this.name = item.name;
            this.username = item.username;
            this.role = item.role;
            this.id_outlet = item.id_outlet;
        },
        Save: function(){
            let config = {
                headers : {
                "Authorization" : "Bearer " + this.$cookies.get('Authorization')
                }
            }

            let form = {
                "name": this.name,
                "username": this.username,
                "password": this.password,
                "role": this.role,
                "id_outlet": this.id_outlet,
            }

            //logika method post/get (insert /update)
            if(this.action == "insert"){
                axios.post(base_url + '/user', form, config)
                .then( response => {
                    alert(response.data.message);
                })
            } else { //update
                axios.put(base_url + '/user/' + this.id, form, config)
                .then( response => {
                    alert(response.data.message);
                })
            }
            
            this.getData();
            
        },
        Delete: function(id){
           if(confirm("Apakah anda yakin menghapus data user ini?")){

                let config = {
                    headers : {
                    "Authorization" : "Bearer " + this.$cookies.get('Authorization')
                    }
                }

                axios.delete(base_url + '/user/' + id, config)
                .then( response => {
                    alert(response.data.message);
                })

                this.getData();
           }

            // Swal.fire({
            //     title: 'Error!',
            //     text: 'Do you want to continue',
            //     icon: 'error',
            //     confirmButtonText: 'Cool'
            // })
        }

    },
    mounted() {
        this.getData();
        this.OutletDropdown();
    },
}
</script>