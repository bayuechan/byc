<template>
  <div class="container bg-dark">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">
            <button @click="showInput" class="btn btn-secondary">输入数据</button> |
            <button @click="showQuery" class="btn btn-secondary">查询</button>
          </div>
          <div class="card-body">
            <!-- 成功输入数据提示 -->
            <modal v-if="isModalVisible" @close="isModalVisible = false">
              <h3 slot="header">
                <i class="fas fa-thumbs-up"></i>成功
              </h3>
              <h4 slot="body">输入数据成功！</h4>
            </modal>
            <!-- 成功删除数据提示 -->
            <modal v-if="wasDeleted" @close="wasDeleted = false">
              <h3 slot="header">
                <i class="fas fa-thumbs-up"></i>成功
              </h3>
              <h4 slot="body">成功删除数据！</h4>
            </modal>
            <!-- 输入数据表格 -->
            <form @submit.prevent="addEvaluation">
              <div class="form-group" v-if="isInput">
                <label for="SelectType">选择评估类型</label>
                <div class="input-group mb-3 input-group-sm">
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
                  <div class="input-group input-group-sm mb-3">
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

                <div class="form-group input-group-sm mb-3">
                  <label for="InputNote">备注</label>
                  <textarea
                    required
                    type="text"
                    class="form-control"
                    id="InputNote"
                    v-model="evaluation.Note"
                  ></textarea>
                </div>
                <button type="submit" id="input_submit" class="btn btn-primary">
                  <i class="fas fa-check-circle"></i>
                  <span></span>提交
                </button>
              </div>
            </form>

            <hr>
            <!-- 查询数据 -->
            <div v-if="!isInput" class="form-group">
              <form @submit.prevent="query">
                <label for="InputDate">选择日期</label>
                <div class="input-group input-group-sm mb-3">
                  <input required type="month" class="form-control" v-model="mydate" id="InputDate">

                  <div class="input-group-append">
                    <button type="submit" class="btn btn-primary">
                      <i class="fas fa-check-circle"></i>
                      <span></span>提交
                    </button>
                  </div>
                </div>
              </form>
              <!-- 显示所计算出的数据 -->
              <div class="form-row align-items-center">
                <div class="col-sm-3 my-1">
                  <label for="interview">面试</label>
                  <div class="input-group input-group-sm mb-3">
                    <input
                      type="text"
                      class="form-control"
                      id="interview"
                      disabled
                      v-model="rows_interviews"
                    >
                    <div class="input-group-append">
                      <span class="input-group-text">x100</span>
                    </div>
                  </div>
                </div>
                <div class="col-sm-3 my-1">
                  <label for="interview-evaluation">面试评估</label>
                  <div class="input-group input-group-sm mb-3">
                    <input
                      type="text"
                      class="form-control"
                      id="interview-evaluation"
                      disabled
                      v-model="rows_interviews_evaluation"
                    >
                    <div class="input-group-append">
                      <span class="input-group-text">x100</span>
                    </div>
                  </div>
                </div>
                <div class="col-sm-3 my-1">
                  <label for="normal-evaluation">普通评估</label>
                  <div class="input-group input-group-sm mb-3">
                    <input
                      type="text"
                      class="form-control"
                      id="normal-evaluation"
                      disabled
                      v-model="rows_normal_evaluation"
                    >
                    <div class="input-group-append">
                      <span class="input-group-text">x80</span>
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-sm-4 my-1">
                <label for="total">合计</label>
                <div class="input-group input-group-sm mb-3">
                  <div class="input-group-prepend">
                    <span class="input-group-text">=</span>
                  </div>
                  <input
                    type="text"
                    class="form-control"
                    id="total"
                    disabled
                    v-model="total"
                  >
                  <div class="input-group-prepend">
                    <span class="input-group-text">元</span>
                  </div>
                </div>
              </div>
            </div>
            <!-- 显示查询数据 -->
            <table v-if="evaluations_query.length>0 && !isInput" class="table table-striped">
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
            <hr>
                <!-- 显示全部数据 -->
                <table v-if="isInput" class="table table-responsive-sm table-striped">
                  <thead>
                    <tr>
                      <th scope="col">Date</th>
                      <th scope="col">Type</th>
                      <th scope="col">Note</th>
                      <th scope="col">Action</th>
                    </tr>
                  </thead>
                  <tbody v-for="evaluation in evaluations.data" :key="evaluation.id">
                 <!-- 确认是否删除数据 -->
                  <div class="modal fade" id="checkModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
                  <div class="modal-dialog modal-dialog-centered" role="document">
                    <div class="modal-content">
                      <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLongTitle">注意！</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                          <span aria-hidden="true">&times;</span>
                        </button>
                      </div>
                      <div class="modal-body">
                        请确认是否要删除此条消息！
                      </div>
                      <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
                        <button type="button" class="btn btn-primary" @click.prevent="deleteEvaluation(evaluation.id)" data-dismiss="modal"> 删除</button>
                      </div>
                    </div>
                  </div>
                </div>

                    <tr>
                      <th scope="row">{{ evaluation.Date }}</th>
                      <td>{{ evaluation.Type }}</td>
                      <td>{{ evaluation.Note }}</td>
                      <td>
                        <button
                          class="btn btn-danger"
                          data-toggle="modal" data-target="#checkModal"
                        >
                          <i class="fas fa-trash-alt"></i>
                        </button>
                      </td>
                    </tr>
                    <pagination :data="evaluations" @pagination-change-page="getEvaluation"></pagination>
                  </tbody>
                </table>
            
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
      evaluation: {},
      evaluations: {},
      evaluations_query: {},
      isModalVisible: false,
      isInput: true,
      wasDeleted: false,
      rows_interviews:0,
      rows_interviews_evaluation:0,
      rows_normal_evaluation:0,
      total:0
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
        this.evaluation = {};
      });
    },
    deleteEvaluation(id) {
      let url = "/evaluations/" + id;
      axios.delete(url).then(response => {
        this.getEvaluation();
        this.showDeleteModal();
      });
    },
    getEvaluation(page = 1) {
      axios("/evaluations?page="+ page)
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
            this.rows_interviews=0;
            this.rows_interviews_evaluation=0;
            this.rows_normal_evaluation=0;
            // 计算相关数据
            this.evaluations_query.forEach(element => {
              if(element.Type == "面试"){
                this.rows_interviews+=1;
              }else if(element.Type == "面试评估"){
                this.rows_interviews_evaluation+=1;
              }else{
                this.rows_normal_evaluation+=1;
              }
            });
            this.total=this.rows_interviews*100+this.rows_interviews_evaluation*100+this.rows_normal_evaluation*80;

          })
          .catch(err => {
            console.log(err);
          });
      }
    }
  }
};
</script>
