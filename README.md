# mpvue-fly
const Fly = require("flyio/dist/npm/wx");
const fly = new Fly();
Vue.prototype.$http = fly;
  created() {
        let params = {}; // url参数

    // 发送请求前的全局拦截器
    fly.interceptors.request.use(request => {
      //给所有请求添加自定义header
      if (this.$globalData.token) {
      params['xxx'] = 'xxx'
      }

      if (this.$globalData.securityToken) {
       params['yy'] = 'yyy'
      }
      params["Content-Type"] = "application/json";
      params["Accept"] = "application/json";

      (request.timeout = 30000), (request.headers = params);
      return request;
    });

    // 响应数据的全局拦截器
    fly.interceptors.response.use(
      response => {
        mpvue.hideLoading();
        if (response.data.success) {
          return response.data.result;
        } else {
          return { errMessage: response.data.message };
        }
      },
      err => {
        mpvue.hideLoading();
        if (err.status == 0) {
          return "网络连接异常";
        } else if (err.status == 1) {
          return "网络连接超时";
        } else if (err.status === 401) {
          //未登录处理
        } else {
          let errTxt = null;
          if (err.response.data) {
            errTxt = err.response.data.error.message;
          } else {
            errTxt = "数据请求失败,请稍后再试";
          }
          mpvue.showToast({
            title: errTxt,
            icon: "none",
            mask: true,
            duration: 3000
          });
          return err.response.data.error;
        }
      }
    )
  }
