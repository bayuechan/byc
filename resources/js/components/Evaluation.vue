<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">评估 |  <button id="show-modal" @click="showModal">Show Modal</button></div>
          <div class="card-body">
                <!-- use the modal component, pass in the prop -->
            <modal v-if="isModalVisible" @close="isModalVisible = false">
                <!--
                you can use custom content here to overwrite
                default content
                -->
                <h3 slot="header">查询</h3>
                <div slot="body" class="form-group">
                <label for="InputDate">选择日期</label>
                <div class="input-group mb-3">
                    <input
                  type="date"
                  class="form-control"
                  id="InputDate"
                >
                
                <div class="input-group-append">
                    <label class="input-group-text" for="inputGroupSelect02">必选</label>
                </div>
                </div>
                </div>

            </modal>
           
            <form>
              <div class="form-group">

                <label for="inputGroupSelect02">选择评估类型</label>
                <div class="input-group mb-3">
                <select class="custom-select" id="inputGroupSelect02">
                    <option selected>选择类型</option>
                    <option value="1">面试</option>
                    <option value="2">面试评估</option>
                    <option value="3">普通评估</option>
                    <div role="separator" class="dropdown-divider"></div>
                    <option class="dropdown-item" href="#">其它</option>
                </select>
                <div class="input-group-append">
                    <label class="input-group-text" for="inputGroupSelect02">必选</label>
                </div>
                </div>
                <div class="form-group mb-3">
                <label for="InputNote">备注</label>
                <textarea
                  type="text"
                  class="form-control"
                  id="InputNote"
                ></textarea>
              </div>
              </div>
              <button type="submit" class="btn btn-primary"><i class="fas fa-check-circle "></i><span></span>提交</button>
            </form>
            
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    console.log(this.isModalVisible);
  },
  data () {
      return {
        isModalVisible: false,
      };
    },
    methods: {
      showModal() {
        this.isModalVisible = true;
      },
      closeModal() {
        this.isModalVisible = false;
      }
    },
};
</script>
