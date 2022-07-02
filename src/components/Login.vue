<template>
    <div>
        <div id="layoutAuthentication">
            <div id="layoutAuthentication_content">
                <main>
                    <div class="container">
                        <div class="row justify-content-center">
                            <div class="col-lg-5">
                                <div class="card shadow-lg border-0 rounded-lg mt-5">
                                    <div class="card-header"><h3 class="text-center font-weight-light my-4">Login</h3></div>
                                    <div class="card-body">
                                        {{ message }}
                                        <form v-on:submit.prevent="Login" method="post">
                                            <div class="form-floating mb-3">
                                                <input v-model="username" class="form-control" id="username" type="text" placeholder="Username" />
                                                <label for="username">Username</label>
                                            </div>
                                            <div class="form-floating mb-3">
                                                <input v-model="password" class="form-control" id="inputPassword" type="password" placeholder="Password" />
                                                <label for="inputPassword">Password</label>
                                            </div>
                                            <div class="d-flex align-items-center justify-content-between mt-4 mb-0">
                                                <input type="submit" value="Login" class="btn btn-primary">
                                            </div>
                                        </form>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </main>
            </div>
            
        </div>
    </div>
</template>

<script>
module.exports =  {
    data: function(){
        return {
            username: "",
            password: "",
            message: "",
        }
    },
    methods: {
        Login: function(){
            this.message = "Mohon tunggu..."; //alert notif

            //mapping data
            let form = {
                "username": this.username,
                "password": this.password
            }

            axios.post(base_url + '/login', form)
            .then(response => {
                if(response.data.success == true){
                    this.message = response.data.message; //alert

                    
                    //cek apakah token sudah ada di cookies???
                    if(this.$cookies.isKey('Authorization')){
                        this.$cookies.remove('Authorization');
                    }

                    //menyimpan token ke cookies
                    this.$cookies.set('Authorization', response.data.data.token);

                    location.reload();
                } else {
                    this.message = response.data.message;
                }

            })


        },

    }
}
</script>