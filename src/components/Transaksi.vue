<template>
  <div>
    <main>
      <div class="container-fluid px-4">
        <div class="card mb-4">
          <div class="card-body">
            <router-link
              to="/input-transaksi"
              class="nav-link btn btn-sm btn-success btn-icon-text"
              v-if="role !== 'owner'"
            >
              <i class="mdi mdi-plus btn-icon-prepend"></i>
              <span class="menu-title">Tambah Transaksi</span>
            </router-link>
            <form>
              <div class="row">
                <div class="col-lg-6 col-md-6">
                  <div class="form-group">
                    <label for="tahun" class="col-form-label">Tahun</label>
                    <b-form-select
                      class="form-control"
                      @change="getData($event)"
                      v-model="tahun"
                      :options="list_years"
                    ></b-form-select>
                  </div>
                </div>
                <div class="col-lg-6 col-md-6">
                  <div class="form-group">
                    <label for="tahun" class="col-form-label">Bulan</label>
                    <b-form-select
                      class="form-control"
                      @change="getData($event)"
                      v-model="bulan"
                      :options="list_months"
                    ></b-form-select>
                  </div>
                </div>
              </div>
            </form>

            <b-table :items="transaksi" :fields="fields_transaksi">
              <template v-slot:cell(status)="data">
                <select
                  class="form-control"
                  @change="changeStatus(data.item.id_transaksi, $event)"
                >
                  <option value="baru" :selected="data.item.status === 'baru'">
                    Baru
                  </option>
                  <option
                    value="proses"
                    :selected="data.item.status === 'proses'"
                  >
                    Proses
                  </option>
                  <option
                    value="selesai"
                    :selected="data.item.status === 'selesai'"
                  >
                    Selesai
                  </option>
                  <option
                    value="diambil"
                    :selected="data.item.status === 'diambil'"
                  >
                    Diambil
                  </option>
                </select>
              </template>

              <template v-slot:cell(dibayar)="data">
                <select
                  class="form-control"
                  @change="changeBayar(data.item.id_transaksi, $event)"
                >
                  <option
                    value="dibayar"
                    :selected="data.item.dibayar === 'belum_dibayar'"
                  >
                    Dibayar
                  </option>
                  <option
                    value="belum_dibayar"
                    :selected="data.item.dibayar === 'dibayar'"
                  >
                    Belum Dibayar
                  </option>
                </select>
              </template>

              <template v-slot:cell(Aksi)="data">
                <b-button
                  size="sm"
                  class="btn btn-sm btn-warning btn-icon-text"
                  v-on:click="Detail(data.item)"
                  v-b-modal.modal-detail
                >
                  <i
                    class="mdi mdi-file-document-box-outline btn-icon-prepend"
                  ></i>
                  Detail
                </b-button>
              </template>
            </b-table>
            <b-toast id="message" title="Message">
              <strong class="text-success">{{ message }}</strong>
            </b-toast>
          </div>
        </div>
      </div>
    </main>

    <b-modal
      id="modal-detail"
      ref="modal"
      title="Detail Transaksi"
      size="md"
      hide-footer="true"
    >
      <div class="table-responsive table table-stripped" id="print">
        <table class="table table-bordered table-responsive">
          <tr>
            <td rowspan="3">
              <img src="src/assets/img/78.jpg" width="100" />
            </td>
            <td>ID ORDER</td>
            <td>:</td>
            <td>{{ detail_transaksi.id_transaksi }}</td>
            <td>NAMA MEMBER</td>
            <td>:</td>
            <td>{{ detail_transaksi.nama_member }}</td>
          </tr>
          <tr>
            <td>NAMA KASIR</td>
            <td>:</td>
            <td>{{ detail_transaksi.kasir }}</td>
            <td>PEMBAYARAN</td>
            <td>:</td>
            <td>{{ detail_transaksi.dibayar }}</td>
          </tr>
          <tr>
            <td>TGL TRANSAKSI</td>
            <td>:</td>
            <td>{{ detail_transaksi.tgl }}</td>
            <td>TGL BAYAR</td>
            <td>:</td>
            <td>{{ detail_transaksi.tgl_bayar }}</td>
          </tr>
        </table>
        <hr />
        <br />
        <b-table
          :items="detail_transaksi.detail_transaksi"
          :fields="fields_detail_transaksi"
        >
        </b-table>
        <div class="text-right">
          <h4>Total: Rp{{ total }}</h4>
        </div>
      </div>
      <br />
      <a href="#" class="btn btn-primary btn-sm" @click="Prints()"
        >Cetak Nota</a
      >
    </b-modal>
  </div>
</template>

<script>
module.exports = {
  data: function () {
    return {
      id_transaksi: "",
      id_mamber: "",
      tgl: "",
      status: "",
      dibayar: "",
      id_user: "",
      transaksi: [],
      fields_transaksi: [
        "id_transaksi",
        "nama_member",
        "tgl",
        "status",
        "dibayar",
        "tgl_bayar",
        "kasir",
        "total",
        "Aksi",
      ],
      fields_detail_transaksi: ["jenis", "berat", "sub_total"],
      detail_transaksi: [],
      action: "",
      message: "",
      total: "",
      bulan: "",
      tahun: "",
      role: "",
      list_years: [2020, 2021, 2022, 2023],
      list_months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
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
    getData: function () {
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
            this.transaksi = response.data.data;
          }
        });
    },
    changeBayar: function (id_transaksi, event) {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };
      let form = {
        id_transaksi: id_transaksi,
        status: event.target.value,
      };
      axios
        .put(base_url + "/transaksi/bayar", form, config)
        .then((response) => {
          this.getData();
          this.message = response.data.message;
          this.$bvToast.show("message");
        })
        .catch((error) => {
          console.log(error);
        });
    },
    changeStatus: function (id_transaksi, event) {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };
      let form = {
        id_transaksi: id_transaksi,
        status: event.target.value,
      };
      axios
        .put(base_url + "/transaksi/status", form, config)
        .then((response) => {
          this.getData();
          this.message = response.data.message;
          this.$bvToast.show("message");
        })
        .catch((error) => {
          console.log(error);
        });
    },
    Detail: function (data) {
      this.total = data.total;
      this.detail_transaksi = data;
    },
    Prints: function () {
      const prtHtml = document.getElementById("print").innerHTML;
      //console.log(prtHtml);
      let stylesHtml = "";
      for (const node of [
        ...document.querySelectorAll('link[rel="stylesheet"], style'),
      ]) {
        stylesHtml += node.outerHTML;
      }

      const WinPrint = window.open(
        "",
        "",
        "left=0,top=0,width=800,height=900,toolbar=0,scrollbars=0,status=0"
      );

      WinPrint.document.write(`<!DOCTYPE html>
      <html>
        <head>
        <link rel="stylesheet" href="src/assets/css/bootstrap.min.css">
          
        </head>
        <body>
          ${prtHtml}
        </body>
      </html>`);

      WinPrint.document.close();
      WinPrint.focus();
      // WinPrint.print();
      // WinPrint.close();
    },
  },
  mounted() {
    this.getInfo();
    this.getData();
  },
};
</script>