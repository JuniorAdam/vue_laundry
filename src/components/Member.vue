<template>
  <div>
    <main>
      <div class="container-fluid px-4">
        <div class="card mb-4">
          <div class="card-body">
            <a class="btn btn-success" v-b-modal.modal_member @click="Add()"
              >Tambah Member</a
            >
            <table class="table">
              <tr>
                <td>ID MEMBER</td>
                <td>NAMA</td>
                <td>Jenis Kelamin</td>
                <td>TLP</td>
                <td>ALAMAT</td>
                <td>AKSI</td>
              </tr>
              <tr v-for="mem in member" :key="mem">
                <td>{{ mem.id_member }}</td>
                <td>{{ mem.nama }}</td>
                <td>
                  <span v-if="mem.jenis_kelamin === 'l'">Laki-Laki</span>
                  <span v-if="mem.jenis_kelamin === 'p'">Perempuan</span>
                </td>
                <td>{{ mem.telp }}</td>
                <td>{{ mem.alamat }}</td>
                <td>
                  <a
                    v-b-modal.modal_member
                    href="#"
                    class="btn btn-info"
                    @click="Edit(mem)"
                    >Ubah</a
                  >
                  <a
                    href="#"
                    class="btn btn-danger"
                    @click="Delete(mem.id_member)"
                    >Hapus</a
                  >
                </td>
              </tr>
            </table>
          </div>
        </div>
      </div>
    </main>

    <b-modal
      id="modal_member"
      ref="modal"
      title="Form Member"
      size="md"
      @ok="Save"
    >
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
        </div>
        <div class="form-floating mb-3 mb-md-0">
          <input
            v-model="tlp"
            placeholder="Enter your first name"
            v-modal="tlp"
            id="inputTlp"
            class="form-control"
            type="text"
          />
          <label for="inputTlp">Telepon</label>
          <p></p>
        </div>
        <div class="form-floating mb-3 mb-md-0">
          <input
            v-model="alamat"
            placeholder="Enter your first name"
            v-modal="alamat"
            id="inputAlamat"
            class="form-control"
            type="text"
          />
          <label for="inputAlamat">Alamat</label>
          <p></p>
        </div>
        <div class="form-floating mb-3 mb-md-0">
          <p>
            <label>Jenis Kelamin:</label>
            <label for="inputJk"
              ><input
                v-model="jk"
                v-modal="jk"
                id="inputJk"
                type="radio"
                value="l"
              />Laki-laki</label
            >
            <label for="inputJk"
              ><input
                v-model="jk"
                v-modal="jk"
                id="inputJk"
                type="radio"
                value="p"
              />Perempuan</label
            >
          </p>
        </div>
      </form>
    </b-modal>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      id_member: "",
      nama: "",
      jk: "",
      tlp: "",
      alamat: "",
      member: [],
      action: "",
      role: "",
    };
  },
  methods: {
    getData: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/member", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.member = response.data.data.member;
        }
      });
    },
    Add: function () {
      this.action = "insert";
      this.id_member = "";
      this.nama = "";
      this.jk = "";
      this.tlp = "";
      this.alamat = "";
    },
    Edit: function (item) {
      this.action = "update";
      this.id_member = item.id_member;
      this.nama = item.nama;
      this.jk = item.jenis_kelamin;
      this.tlp = item.telp;
      this.alamat = item.alamat;
    },
    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        nama: this.nama,
        alamat: this.alamat,
        jenis_kelamin: this.jk,
        telp: this.tlp,
      };

      //logika method post/get (insert /update)
      if (this.action == "insert") {
        axios.post(base_url + "/member", form, config).then((response) => {
          swal.fire(response.data.message);
        });
      } else {
        //update
        axios
          .put(base_url + "/member/" + this.id_member, form, config)
          .then((response) => {
            swal.fire(response.data.message);
          });
      }

      this.getData();
    },
    Delete: function (id) {
      Swal.fire({
        text: "Apakah anda yakin untuk menghapus member ini?",
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

          axios.delete(base_url + "/member/" + id, config).then((response) => {
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