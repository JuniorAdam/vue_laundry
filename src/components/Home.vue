<template>
  <main>
    <div class="container-fluid py-3">
      <div class="row">
        <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4" v-if="role !== 'owner'">
          <div class="card">
            <div class="card-body p-5">
              <div class="row">
                <div class="col-8">
                  <div class="numbers">
                    <p class="text-sm mb-0 text-uppercase font-weight-bold">
                      Jumlah Member
                    </p>
                    <h5 class="font-weight-bolder">{{ member }}</h5>
                  </div>
                </div>
                <div class="col-4 text-end">
                  <div
                    class="
                      icon icon-shape
                      bg-gradient-primary
                      shadow-primary
                      text-center
                      rounded-circle
                    "
                  >
                    <i class="fa fa-user text-lg opacity-10"></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4" v-if="role === 'admin'">
          <div class="card">
            <div class="card-body p-5">
              <div class="row">
                <div class="col-8">
                  <div class="numbers">
                    <p class="text-sm mb-0 text-uppercase font-weight-bold">
                      Jumlah Cabang
                    </p>
                    <h5 class="font-weight-bolder">{{ outlet }}</h5>
                  </div>
                </div>
                <div class="col-4 text-end">
                  <div
                    class="
                      icon icon-shape
                      bg-gradient-danger
                      shadow-danger
                      text-center
                      rounded-circle
                    "
                  >
                    <i class="ni ni-world text-lg opacity-10"></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
          <div class="card">
            <div class="card-body p-5">
              <div class="row">
                <div class="col-8">
                  <div class="numbers">
                    <p class="text-sm mb-0 text-uppercase font-weight-bold">
                      Jumlah Transaksi
                    </p>
                    <h5 class="font-weight-bolder">{{ transaksi }}</h5>
                  </div>
                </div>
                <div class="col-4 text-end">
                  <div
                    class="
                      icon icon-shape
                      bg-gradient-success
                      shadow-success
                      text-center
                      rounded-circle
                    "
                  >
                    <i
                      class="ni ni-cart text-lg opacity-10"
                      aria-hidden="true"
                    ></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <router-link
      to="/input-transaksi"
      class="nav-link btn btn-sm btn-success btn-icon-text"
      v-if="role !== 'owner'"
    >
      <i class="mdi mdi-plus btn-icon-prepend"></i>
      <span class="menu-title">Tambah Transaksi</span>
    </router-link>
  </main>
</template>
<script>
module.exports = {
  data: function () {
    return {
      member: "",
      outlet: "",
      transaksi: "",
      role: "",
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
            this.role = response.data.data.role;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getOutlet: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/outlet", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.outlet = response.data.data.count;
        }
      });
    },
    getMember: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/member", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.member = response.data.data.count;
        }
      });
    },
    getTransaksi: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        tahun: this.tahun,
        bulan: this.bulan,
      };

      axios
        .post(base_url + "/transaksi/report", form, config)
        .then((response) => {
          console.log(response);
          if (response.data.success == true) {
            this.transaksi = response.data.data.length;
          }
        });
    },
  },
  mounted() {
    this.getInfo();
    this.getOutlet();
    this.getMember();
    this.getTransaksi();
  },
};
</script>