<template>
    <div>
        <main>
            <div class="container-fluid px-4">
                <h1 class="mt-4">Data Outlet</h1>
                <div class="card mb-4">
                    <div class="card-body" >
                        <a class="btn btn-success" v-if="role !== 'admin'" v-b-modal.modal_outlet @click="Add()" >Tambah Outlet</a>
                        <table class="table mt-2">
                            <tr class="table-dark">
                                <td>ID OUTLET</td>
                                <td>NAMA</td>
                                <td>AKSI</td>
                            </tr>
                            <tr v-for="(out, index) in outlet" :key="index">
                                <td>{{ index + 1 }}</td>
                                <td>{{ out.name_outlet }}</td>
                                <td>
                                    <a v-if="role !== 'admin'" v-b-modal.modal_outlet href="#" class="btn btn-info" @click="Edit(out)">Ubah</a>
                                    <a v-if="role !== 'admin'" href="#" class="btn btn-danger" @click="Delete(out.id_outlet)">Hapus</a>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
            </div>
        </main>

        <b-modal 
            id="modal_outlet" 
            ref="modal" 
            title="Form Outlet" 
            size="md" 
            @ok="Save">
            <form>
                <div class="form-floating mb-3 mb-md-0">
                    <input v-model="name_outlet" placeholder="Enter your first name" v-modal="name" id="inputname" class="form-control" type="text"/>
                    <label for="inputname">Nama Outlet</label>
                </div>
            </form>
        </b-modal>
    </div>
</template>
<script>
module.exports =  {
    data: function(){
        return {
            id_outlet: "",
            name_outlet: "",
            outlet: [],
            action:"",
            role: "",
        }
    },
    methods: {
        getData: function(){
            let config = {
                headers : {
                "Authorization" : "Bearer " + this.$cookies.get('Authorization')
                }
            }

          axios.get(base_url + '/outlet', config)
          .then( response => {
              console.log(response);
            if(response.data.success == true){
                this.outlet = response.data.data.outlet;
            }
          })

        },
        Add: function(){
            this.action = "insert";
            this.id_outlet = "";
            this.name_outlet = "";
        },
        Edit: function(item){
            this.action = "update";
            this.id_outlet = item.id_outlet;
            this.name_outlet = item.name_outlet;
        },
        Save: function(){
            let config = {
                headers : {
                "Authorization" : "Bearer " + this.$cookies.get('Authorization')
                }
            }

            let form = {
                "name_outlet": this.name_outlet,
            }

            //logika method post/get (insert /update)
            if(this.action == "insert"){
                axios.post(base_url + '/outlet', form, config)
                .then( response => {
                    alert(response.data.message);
                })
            } else { //update
                axios.put(base_url + '/outlet/' + this.id_outlet, form, config)
                .then( response => {
                    alert(response.data.message);
                })
            }
            
            this.getData();
            
        },
        Delete: function(id){
           if(confirm("Apakah anda yakin menghapus data outlet ini?")){

                let config = {
                    headers : {
                    "Authorization" : "Bearer " + this.$cookies.get('Authorization')
                    }
                }

                axios.delete(base_url + '/outlet/' + id, config)
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
    },
}
</script>