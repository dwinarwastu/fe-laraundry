<template>
<div>
    <nav class="sb-topnav navbar navbar-expand navbar-dark bg-dark">
        <!-- Navbar Brand-->
        <a class="navbar-brand ps-3">LARAUNDRY</a>
        <!-- Navbar-->
        <ul class="navbar-nav position-absolute end-0 mr-auto ms-md-0 me-3 me-lg-4">
            <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" id="navbarDropdown" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false"><i class="fas fa-user-check"></i>
                {{username}}
                </a>
                <ul class="dropdown-menu dropdown-menu-end" aria-labelledby="navbarDropdown">
                    <li><a @click="Logout" class="dropdown-item" href="#">Logout</a></li>
                </ul>
            </li>
        </ul>
    </nav>

    <div id="layoutSidenav">
        <div id="layoutSidenav_nav">
            <nav class="sb-sidenav accordion sb-sidenav-dark" id="sidenavAccordion">
                <div class="sb-sidenav-menu">
                    <div class="nav">
                        <router-link class="nav-link" to="/">
                            <div class="sb-nav-link-icon">
                                <i class="fas fa-home"></i>
                            </div>
                            Home
                        </router-link>
                        <router-link class="nav-link collapsed" to="/member" v-if="role !== 'owner'">
                            <div class="sb-nav-link-icon"><i class="fas fa-users"></i></div>
                            Member
                        </router-link>
                        <router-link class="nav-link collapsed" to="/outlet" v-if="role === 'admin'">
                            <div class="sb-nav-link-icon"><i class="fas fa-store"></i></div>
                            Outlet
                        </router-link>
                        <router-link class="nav-link collapsed" to="/paket" v-if="role === 'admin'">
                            <div class="sb-nav-link-icon"><i class="fas fa-boxes"></i></div>
                            Paket
                        </router-link>
                        <router-link class="nav-link collapsed" to="/user" v-if="role === 'admin'">
                            <div class="sb-nav-link-icon"><i class="fas fa-user"></i></div>
                            User
                        </router-link>
                        <router-link class="nav-link collapsed" to="/transaksi">
                            <div class="sb-nav-link-icon"><i class="fas fa-cash-register"></i></div>
                            Transaksi
                        </router-link>

                    </div>
                </div>
                <!-- <div class="sb-sidenav-footer">
                    <div class="small">logged in as:</div>
                    {{ username }}
                </div> -->
            </nav>
        </div>

        <div id="layoutSidenav_content">

            <!-- MAIN -->
            <router-view>

            </router-view>

            <footer class="py-4 bg-light mt-auto">
                <div class="container-fluid px-4">
                    <div class="d-flex align-items-center justify-content-between small">
                        <div class="text-muted">Copyright &copy; Dwi 2022</div>
                        <div>
                            <a href="#">Privacy Policy</a>
                            &middot;
                            <a href="#">Terms &amp; Conditions</a>
                        </div>
                    </div>
                </div>
            </footer>
        </div>
    </div>
</div>
</template>
<script>
module.exports = {
    data: function(){
        return {
            username: "",
            role: "",
        }
    },
    methods: {
        getInfo: function(){
          let config = {
            headers : {
              "Authorization" : "Bearer " + this.$cookies.get('Authorization')
            }
          }

          axios.get(base_url + '/login/check', config)
          .then( response => {
            if(response.data.success == true){
              this.username = response.data.data.username;
              this.role = response.data.data.role;
            }
          })

        },
        Logout: function(){
            this.$cookies.remove("Authorization");
            this.componentName = "login";

            document.location.href = "/ukk_laundry"
        }
    },
    mounted() {
        this.getInfo();
    },
}
</script>