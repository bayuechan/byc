<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">
            <button @click="showInput" class="btn btn-secondary">输入数据</button> |
            <button @click="showQuery" class="btn btn-secondary">查询</button>
          </div>
          <div class="card-body">
            <modal v-if="isModalVisible" @close="isModalVisible = false">
                <h3 slot="header"><i class="fas fa-thumbs-up" ></i>成功</h3>
                <h4 slot="body">输入数据成功！</h4>
            </modal>
            <modal v-if="wasDeleted" @close="wasDeleted = false">
                <h3 slot="header"><i class="fas fa-thumbs-up" ></i>成功</h3>
                <h4 slot="body">成功删除数据！</h4>
            </modal>
            <form @submit.prevent="addEvaluation">
              <div class="form-group" v-if="isInput">
                <label for="SelectType">选择评估类型</label>
                <div class="input-group mb-3">
                  <select class="custom-select" id="SelectType" v-model="evaluation.Type">
                    <option value="面试">面试</option>
                    <option value="面试评估">面试评估</option>
                    <option value="普通评估">普通评估</option>
                  </select>
                  <div class="input-group-append">
                    <label class="input-group-text" for="inputGroupSelect02">必选</label>
                  </div>
                </div>

                <div class="input-group">
                  <label for="SelectDate">选择日期</label>
                  <div class="input-group mb-3">
                    <input
                      required
                      type="date"
                      class="form-control"
                      v-model="evaluation.Date"
                      id="SelectDate"
                    >

                    <div class="input-group-append">
                      <label class="input-group-text" for="SelectDate">必选</label>
                    </div>
                  </div>
                </div>

                <div class="form-group mb-3">
                  <label for="InputNote">备注</label>
                  <textarea
                    required
                    type="text"
                    class="form-control"
                    id="InputNote"
                    v-model="evaluation.Note"
                  ></textarea>
                </div>
                <button type="submit" class="btn btn-primary">
                  <i class="fas fa-check-circle"></i>
                  <span></span>提交
                </button>
                <hr>

                <table class="table table-striped">
                  <thead>
                    <tr>
                      <th scope="col">Date</th>
                      <th scope="col">Type</th>
                      <th scope="col">Note</th>
                      <th scope="col">Action</th>
                    </tr>
                  </thead>
                  <tbody v-for="evaluation in evaluations" :key="evaluation.id">
                    <tr>
                      <th scope="row">{{ evaluation.Date }}</th>
                      <td>{{ evaluation.Type }}</td>
                      <td>{{ evaluation.Note }}</td>
                      <td><button class="btn btn-danger" @click.prevent="deleteEvaluation(evaluation.id)"><i class="fas fa-trash-alt"></i></button></td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </form>

            <div v-if="!isInput" class="form-group">
              <form @submit.prevent="query">
                <label for="InputDate">选择日期</label>
                <div class="input-group mb-3">
                  <input required type="month" class="form-control" v-model="mydate" id="InputDate">

                  <div class="input-group-append">
                    <button type="submit" class="btn btn-primary">
                      <i class="fas fa-check-circle"></i>
                      <span></span>提交
                    </button>
                  </div>
                </div>
              </form>
              <table v-if="evaluations_query.length>0" class="table table-striped">
                <thead>
                  <tr>
                    <th scope="col">Date</th>
                    <th scope="col">Type</th>
                    <th scope="col">Note</th>
                  </tr>
                </thead>
                <tbody v-for="evaluation in evaluations_query" :key="evaluation.id">
                  <tr>
                    <th scope="row">{{ evaluation.Date }}</th>
                    <td>{{ evaluation.Type }}</td>
                    <td>{{ evaluation.Note }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    this.getEvaluation();
  },
  data() {
    return {
      mydate: "",
      rows:0,
      evaluation: {},
      evaluations: {},
      evaluations_query: {},
      isModalVisible: false,
      isInput: true,
      wasDeleted:false,
    };
  },
  methods: {
    showQuery() {
      this.isInput = false;
    },
    showInput() {
      this.isInput = true;
    },
    showModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    showDeleteModal() {
      this.wasDeleted = true;
    },
    closeDeleteModal() {
      this.wasDeleted = false;
    },
    addEvaluation() {
      axios.post("/evaluations", this.evaluation).then(response => {
        this.getEvaluation();
        this.showModal();
        this.evaluation={};
      });
    },
    deleteEvaluation(id){
      let url = '/evaluations/'+id;
      axios.delete(url).then(response => {
        this.getEvaluation();
        this.showDeleteModal();
      });

    },
    getEvaluation(){
      axios("/evaluations")
      .then(res => {
        this.evaluations = res.data;
      })
      .catch(err => {
        console.log(err);
      });
    },
    query() {
      if (this.mydate) {
        axios("/evaluations/" + this.mydate)
          .then(res => {
            this.evaluations_query = res.data;
          })
          .catch(err => {
            console.log(err);
          });
      }
    }
  }
};
</script>
