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
            <!-- <modal v-if="isModalVisible" @close="isModalVisible = false">
                <h3 slot="header">查询</h3>
            </modal>-->
            <form>
              <div class="form-group" v-if="isInput">
                <label for="inputGroupSelect02">选择评估类型</label>
                <div class="input-group mb-3">
                  <select class="custom-select" id="inputGroupSelect02">
                    <option value="1">面试</option>
                    <option value="2">面试评估</option>
                    <option value="3">普通评估</option>
                  </select>
                  <div class="input-group-append">
                    <label class="input-group-text" for="inputGroupSelect02">必选</label>
                  </div>
                </div>
                <div class="form-group mb-3">
                  <label for="InputNote">备注</label>
                  <textarea type="text" class="form-control" id="InputNote"></textarea>
                </div>
                <button type="submit" class="btn btn-primary">
                  <i class="fas fa-check-circle"></i>
                  <span></span>提交
                </button>

                <table class="table table-striped">
              <thead>
                <tr>
                  <th scope="col">Date</th>
                  <th scope="col">Type</th>
                  <th scope="col">Note</th>
                </tr>
              </thead>
              <tbody v-for="evaluation in evaluations" :key="evaluation.id">
                <tr>
                  <th scope="row">{{ evaluation.Date }}</th>
                  <td>{{ evaluation.Type }}</td>
                  <td>{{ evaluation.Note }}</td>
                </tr>
              
              </tbody>
            </table>
              </div>
              

            </form>

            

            <div v-if="!isInput" class="form-group">
              <form v-on:submit.prevent="query">
              <label for="InputDate">选择日期</label>
              <div class="input-group mb-3">
                <input required type="date" class="form-control" id="InputDate">

                <div class="input-group-append">
                  <button type="submit" class="btn btn-primary">提交</button>
                </div>
              </div>
              </form>
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
    axios('/evaluations')
         .then(res => {
           this.evaluations=res.data;

        })
        .catch(err => {    
            console.log(err);
        });
  },
  data() {
    return {
      mydate:'',
      evaluations:{},
      isModalVisible: false,
      isInput: true
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
    query(){
      if(this.mydate){
        console.log(this.mydate);
      }
      console.log(this.mydate);
    }
  }
};
</script>
