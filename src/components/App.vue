<template>
  <div>
    <aside
      class="
        sidenav
        bg-white
        navbar navbar-vertical navbar-expand-xs
        border-0 border-radius-xl
        my-3 fixed-start ms-4 ps
      "
      id="sidenav-main"
    >
      <div class="sidenav-header">
        <i
          class="
            
            p-3
            cursor-pointer
            text-secondary
            opacity-5
            position-absolute
            end-0
            top-0
            d-xl-none
          "
          id="iconSidenav"
        ></i>
       <a class="navbar-brand m-0">
        <img src="src/assets/img/78.jpg">
        <span class="ms-1 font-weight-bold">Laundry</span>
      </a>
      </div>
      <hr class="horizontal dark mt-0" />
      <ul class="navbar-nav">
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/"
            >
              <div class="icon icon-shape icon-sm border-radius-md text-center me-2 d-flex align-items-center justify-content-center">
              <i class="ni ni-tv-2 text-primary text-sm opacity-10"></i>
            </div>
              <span class="nav-link-text ms-1 font-weight-bold">Dashboard</span>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/member"
              v-if="role !== 'owner'"
            >
              <div class="icon icon-shape icon-sm border-radius-md text-center me-2 d-flex align-items-center justify-content-center">
              <i class="ni ni-calendar-grid-58 text-warning text-sm opacity-10"></i>
            </div>
              <span class="nav-link-text ms-1 font-weight-bold">Member</span>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              href="#"
              to="/outlet"
              v-if="role === 'admin'"
            >
              <div class="icon icon-shape icon-sm border-radius-md text-center me-2 d-flex align-items-center justify-content-center">
              <i class="ni ni-calendar-grid-58 text-warning text-sm opacity-10"></i>
            </div>
              <span class="nav-link-text ms-1 font-weight-bold">Outlet</span>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/paket"
              v-if="role === 'admin'"
            >
              <div class="icon icon-shape icon-sm border-radius-md text-center me-2 d-flex align-items-center justify-content-center">
              <i class="ni ni-calendar-grid-58 text-warning text-sm opacity-10"></i>
            </div>
              <span class="nav-link-text ms-1 font-weight-bold">Paket</span>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/user"
              v-if="role === 'admin'"
            >
              <div class="icon icon-shape icon-sm border-radius-md text-center me-2 d-flex align-items-center justify-content-center">
              <i class="ni ni-calendar-grid-58 text-warning text-sm opacity-10"></i>
            </div>
              <span class="nav-link-text ms-1 font-weight-bold">User</span>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/transaksi"
            >
              <div class="icon icon-shape icon-sm border-radius-md text-center me-2 d-flex align-items-center justify-content-center">
              <i class="ni ni-calendar-grid-58 text-warning text-sm opacity-10"></i>
            </div>
              <span class="nav-link-text ms-1 font-weight-bold">Transaksi</span>
            </router-link>
          </li>
    </aside>

    <main class="main-content position-relative border-radius-lg">
      <nav
        class="
          navbar navbar-main navbar-expand-lg
          px-0
          mx-4
          shadow-none
          border-radius-xl
        "
        id="navbarBlur"
        data-scroll="false"
      >
        <div class="container-fluid py-1 px-3">
          <nav aria-label="breadcrumb">
            <h6 class="font-weight-bolder text-white mb-0">{{ username }}</h6>
            <ol
              class="breadcrumb bg-transparent mb-0 pb-0 pt-1 px-0 me-sm-6 me-5"
            >
              <li class="breadcrumb-item text-sm text-white active">
                {{ role }}
              </li>
            </ol>
          </nav>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item d-flex align-items-center">
              <a>
                <li class="breadcrumb-item text-sm text-white active">
                  <a @click="Logout" href="#!"
                    ><h6 class="font-weight-bolder text-white mb-0">
                      Logout
                    </h6></a
                  >
                </li>
              </a>
            </li>
          </ul>
        </div>
      </nav>
      <p></p>
      <div id="layoutSidenav_content">
        <!-- MAIN -->
        <router-view></router-view>
      </div>
    </main>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      username: "",
      role: "",
      nama_outlet: "",
      id_outlet: "",
    };
  },
  methods: {
    getInfo: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios
        .get(base_url + "/login/check", config)
        .then((response) => {
          if (response.data.success == true) {
            this.username = response.data.data.username;
            this.role = response.data.data.role;
            this.id_outlet = response.data.data.id_outlet;
            axios
              .get(base_url + "/outlet/" + response.data.data.id_outlet, config)

              .then((response2) => {
                if (response2.data.success == true) {
                  this.nama_outlet = response2.data.data.nama_outlet;
                }
              })
              .catch((error) => {
                console.log(error);
              });
          } else {
            this.componentName = "login";
            window.location = front_url;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    Logout: function () {
      this.$cookies.remove("Authorization");
      this.componentName = "login";
      location.reload();
      window.location == front_url;
    },
  },
  mounted() {
    this.getInfo();
  },
};
</script>