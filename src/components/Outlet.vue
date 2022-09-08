<template>
  <div>
    <main>
      <div class="container-fluid px-4">
        <div class="card mb-4">
          <div class="card-body">
            <a class="btn btn-success" v-b-modal.modal_outlet @click="Add()"
              >Tambah Outlet</a
            >
            <table class="table">
              <tr>
                <td>ID Outlet</td>
                <td>Outlet</td>
                <td>Aksi</td>
              </tr>
              <tr v-for="out in outlet" :key="out">
                <td>{{ out.id_outlet }}</td>
                <td>{{ out.nama_outlet }}</td>
                <td>
                  <a
                    v-b-modal.modal_outlet
                    href="#"
                    class="btn btn-info"
                    @click="Edit(out)"
                    >Ubah</a
                  >
                  <a
                    href="#"
                    class="btn btn-danger"
                    @click="Delete(out.id_outlet)"
                    >Hapus</a
                  >
                </td>
              </tr>
            </table>
          </div>
        </div>
      </div>

      <b-modal
        id="modal_outlet"
        ref="modal"
        title="Form Outlet"
        size="md"
        @ok="Save"
      >
        <form>
          <div class="form-floating mb-3 mb-md-0">
            <input
              v-model="nama_outlet"
              placeholder="Enter your first name"
              v-modal="nama_outlet"
              id="inputNama"
              class="form-control"
              type="text"
            />
            <label for="inputNama">Nama Outlet</label>
            <p></p>
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
      id_outlet: "",
      nama_outlet: "",
      outlet: [],
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

      axios.get(base_url + "/outlet", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.outlet = response.data.data.outlet;
        }
      });
    },
    Add: function () {
      this.action = "insert";
      this.id_outlet = "";
      this.nama_outlet = "";
    },
    Edit: function (item) {
      this.action = "update";
      this.id_outlet = item.id_outlet;
      this.nama_outlet = item.nama_outlet;
    },
    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        nama_outlet: this.nama_outlet,
      };

      //logika method post/get (insert /update)
      if (this.action == "insert") {
        axios.post(base_url + "/outlet", form, config).then((response) => {
          swal.fire(response.data.message);
        });
      } else {
        //update
        axios
          .put(base_url + "/outlet/" + this.id_outlet, form, config)
          .then((response) => {
            swal.fire(response.data.message);
          });
      }

      this.getData();
    },
    Delete: function (id) {
      Swal.fire({
        text: "Apakah anda yakin untuk menghapus outlet ini?",
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

          axios.delete(base_url + "/outlet/" + id, config).then((response) => {
            swal.fire(response.data.message);
          });

          this.getData();
        }
      });
    },
  },
  mounted() {
    this.getData();
  },
};
</script>