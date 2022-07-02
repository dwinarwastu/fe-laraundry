<template>
  <div id="poin">
    <div class="content-wrapper">
      <div class="row">
        <div class="col-lg-12 grid-margin stretch-card">
          <div class="card">
            <div class="card-body">
              <h2>Daftar Transaksi</h2>
              <p class="card-description float-right">
                <router-link
                  to="/tambah-transaksi"
                  class="nav-link btn btn-sm btn-success btn-icon-text text-light"
                  style="text-decoration: none;"
                  v-if="role === 'admin' || 'kasir'"
                >
                  <i class="mdi mdi-plus btn-icon-prepend"></i>
                  <span class="menu-title">Tambah Transaksi</span>
                </router-link>
              </p>
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
              <form>
                <table class="table mt-3 " id="dataTable">
                  <tr >
                    <th>ID</th>
                    <th>Nama Member</th>
                    <th>Tanggal</th>
                    <th>Status Cucian</th>
                    <th>Status Pembayaran</th>
                    <th>Tanggal Bayar</th>
                    <th>Kasir</th>
                    <th>Total</th>
                    <th>Aksi</th>
                  </tr>
                  <tr v-for="(saksi, index) in transaksi" :key="index">
                    <td>{{ index + 1 }}</td>
                    <td>{{ saksi.nama_member }}</td>
                    <td>{{ saksi.tgl }}</td>
                    <td>
                      <select
                        class="form-control"
                        id="form_control_status_pembayaran"
                        :disabled="role === 'owner'"
                        @change="changeStatus(saksi.id_transaksi, $event)"
                      >
                        <option
                          value="baru"
                          v-if="saksi.status === 'baru'"
                          :selected="saksi.status === 'baru'"
                        >
                          Baru
                        </option>
                        <option
                          value="proses"
                          :selected="saksi.status === 'proses'"
                        >
                          Proses
                        </option>
                        <option
                          value="selesai"
                          :selected="saksi.status === 'selesai'"
                        >
                          Selesai
                        </option>
                        <option
                          value="diambil"
                          :selected="saksi.status === 'diambil'"
                        >
                          Diambil
                        </option>
                      </select>
                    </td>
                    <td>
                      <select
                        :disabled="role === 'owner'"
                        class="form-control"
                        @change="changeBayar(saksi.id_transaksi, $event)"
                      >
                        <option
                          value="dibayar"
                          :selected="saksi.dibayar === 'dibayar'"
                        >
                          Dibayar
                        </option>
                        <option
                          value="belum_dibayar"
                          :selected="saksi.dibayar === 'belum_dibayar'"
                        >
                          Belum Dibayar
                        </option>
                      </select>
                    </td>
                    <td>{{ saksi.tgl_bayar }}</td>
                    <td>{{ saksi.kasir }}</td>
                    <td>{{ saksi.total }}</td>
                    <td>
                      <a
                        v-b-modal.modal-detail
                        href="#"
                        class="btn btn-warning"
                        @click="Detail(saksi.detail_transaksi, saksi.total)"
                      >
                        Detail
                      </a>
                    </td>
                  </tr>
                </table>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>

    <b-modal
      id="modal-detail"
      ref="modal"
      title="Detail Transaksi"
      size="md"
      hide-footer="true"
    >
      <a href="#" @click="Prints()">CETAK NOTA</a>
      <div class="table-responsive table table-stripped" id="print">
        <b-table
          striped
          hover
          :items="detail_transaksi"
          :fields="fields_detail_transaksi"
        >
        </b-table>
        <div class="text-right">
          <h4>Total: Rp{{ total }}</h4>
        </div>
      </div>
    </b-modal>

    <b-modal
      id="modal-recipe"
      ref="modal"
      title="Nota Pemesanan"
      size="md"
      hide-footer="true"
    >
      <div class="table-responsive">
        <b-table
          striped
          hover
          :items="detail_transaksi"
          :fields="fields_detail_transaksi"
        >
        </b-table>
        <div class="text-right">
          <h4>Total: Rp{{ total }}</h4>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
module.exports = {
  data: function () {
    return {
      tahun: "",
      bulan: "",
      tgl: "",
      action: "",
      message: "",
      key: "",
      total: "",
      transaksi: [],
      detail_transaksi: [],
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
      fields_detail_transaksi: ["jenis", "weight", "sub_total"],
      list_years: [2020, 2021, 2022, 2023],
      list_months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
      role: "",
    };
  },

  methods: {
    getData: function () {
      let conf = { headers: { Authorization: "Bearer " + this.key } };
      let form = {
        tahun: this.tahun,
        bulan: this.bulan,
        tgl: this.tgl,
      };
      axios
        .post(base_url + "/transaksi/report", form, conf)
        .then((response) => {
          this.transaksi = response.data.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    changeStatus: function (id_transaksi, event) {
      let conf = { headers: { Authorization: "Bearer " + this.key } };
      let form = {
        id_transaksi: id_transaksi,
        status: event.target.value,
      };
      axios
        .put(base_url + "/transaksi/status", form, conf)
        .then((response) => {
          this.getData();
          this.message = response.data.message;
          alert(response.data.message)
        })
        .catch((error) => {
            console.log(error);
        });
    },
    changeBayar: function (id_transaksi, event) {
        let conf = { headers: { Authorization: "Bearer " + this.key } };
      let form = {
          id_transaksi: id_transaksi,
        status: event.target.value,
      };
      axios
        .put(base_url + "/transaksi/bayar", form, conf)
        .then((response) => {
            this.getData();
            this.message = response.data.message;
            alert(response.data.message)
          
        })
        .catch((error) => {
          console.log(error);
        });
    },
    Detail: function (detail_transaksi, total) {
      this.total = total;
      this.detail_transaksi = detail_transaksi;
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
      WinPrint.print();
      WinPrint.close();
    },
  },
  mounted() {
    this.key = this.$cookies.get("Authorization");
    this.getData();
  },
};
</script>