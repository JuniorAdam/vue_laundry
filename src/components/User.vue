<template>
  <div>
    <main>
      <div class="container-fluid px-4">
        <div class="card mb-4">
          <div class="card-body">
            <a class="btn btn-success" v-b-modal.modal_user @click="Add()"
              >Tambah User</a
            >
            <table class="table">
              <tr>
                <td>ID</td>
                <td>ID Outlet</td>
                <td>Nama</td>
                <td>Username</td>
                <td>Role</td>
                <td>Aksi</td>
              </tr>
              <tr v-for="use in user" :key="use">
                <td>{{ use.id }}</td>
                <td>{{ use.outlet.nama_outlet }}</td>
                <td>{{ use.nama }}</td>
                <td>{{ use.username }}</td>
                <td>{{ use.role }}</td>
                <td>
                  <a
                    v-b-modal.modal_user
                    href="#"
                    class="btn btn-info"
                    @click="Edit(use)"
                    >Ubah</a
                  >
                  <a href="#" class="btn btn-danger" @click="Delete(use.id)"
                    >Hapus</a
                  >
                </td>
              </tr>
            </table>
          </div>
        </div>
      </div>
      
    </main>
<main>
    <b-modal id="modal_user" ref="modal" title="Form User" size="md" @ok="Save">
      <form>
        <div class="form-floating mb-3 mb-md-0">
          <input
            v-model="nama"
            placeholder="Enter your first name"
            v-modal="nama"
            id="inputNama"
            class="form-control"
            type="text"
          />
          <label for="inputNama">Nama</label>
          <p></p>
          <div class="form-floating mb-3 mb-md-0">
          <input
            v-model="username"
            placeholder="Enter your first name"
            v-modal="username"
            id="inputUsername"
            class="form-control"
            type="text"
          />
          <label for="inputUsername">Username</label>
          <p></p>
          <div class="form-floating mb-3 mb-md-0">
          <input
            v-model="password"
            placeholder="Enter your first name"
            v-modal="password"
            id="inputPassword"
            class="form-control"
            type="password"
          />
          <label for="inputPassword">Password</label>
          <p></p>
          <div class="form-floating mb-3 mb-md-0">
                    <select v-model="role" class="form-control">
                        <option value="admin">Admin</option>
                        <option value="kasir">Kasir</option>
                        <option value="owner">Owner</option>
                    </select>
                    <label for="inputRole">Role</label>
          </div>
          <p></p>
          <div class="form-floating mb-3 mb-md-0">
                    <b-form-select class="form-control" v-model="id_outlet" 
                    :options="nama_outlet"></b-form-select>
                    <label for="id_outlet" class="col-form-label">Outlet</label>
                </div>
      </form>
    </b-modal>
    </main>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      id: "",
      id_outlet: "",
      nama: "",
      username: "",
      password: "",
      role: "",
      nama_outlet: [],
      user: [],
      action: "",
    };
  },
  methods: {
    getData: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/user", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.user = response.data.data.user;
        }
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
        let json_outlet = response.data.data.outlet;
        let list_outlet = [
          {
            value: "",
            text: "--Pilih Outlet--",
          },
        ];
        json_outlet.forEach((element) => {
          list_outlet.push({
            value: element.id_outlet,
            text: element.nama_outlet,
          });
        });
        this.nama_outlet = list_outlet;
      });
    },
    Add: function () {
      this.action = "insert";
      this.id = "";
      this.id_outlet = "";
      this.nama = "";
      this.username = "";
      this.password = "";
      this.role = "";

      // this.getOutlet();
    },
    Edit: function (item) {
      this.action = "update";
      this.id = item.id;
      this.id_outlet = item.id_outlet;
      this.nama = item.nama;
      this.username = item.username;
      this.password = item.password;
      this.role = item.role;

      // this.getOutlet();
    },
    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        nama: this.nama,
        username: this.username,
        password: this.password,
        id_outlet: this.id_outlet,
        role: this.role,
      };

      //logika method post/get (insert /update)
      if (this.action == "insert") {
        axios.post(base_url + "/user", form, config).then((response) => {
          swal.fire(response.data.message);
        });
      } else {
        //update
        axios
          .put(base_url + "/user/" + this.id, form, config)
          .then((response) => {
            swal.fire(response.data.message);
          });
      }

      this.getData();
    },
    Delete: function (id) {
      Swal.fire({
        text: "Apakah anda yakin untuk menghapus user ini?",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Hapus Saja",
      }).then((result) => {
        if (result.isConfirmed) {
          let config = {
            headers: {
              Authorization: "Bearer " + this.$cookies.get("Authorization"),
            },
          };

          axios.delete(base_url + "/user/" + id, config).then((response) => {
            swal.fire(response.data.message);
          });

          this.getData();
        }
      });
    },
  },
  mounted() {
    this.getData();
    this.getOutlet();
  },
};
</script>