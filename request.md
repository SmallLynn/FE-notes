## axios 封装http request示例

##### 请求示例1 文件上传
```js
/* 
 * param -> query param参数
 * reqBody -> formData的请求体
 */
axiosBaseInstance.post(
    `/api/dispatch/ledger/person/add?${utils.transformToURLSearchParams(params)}`,
    reqBody,
    { headers: { 'Content-Type': 'multipart/form-data' } },
),
```
qs.stringify()将对象 序列化成URL的形式，以&进行拼接
```js
import qs from 'qs';

export default {
    transformToURLSearchParams: (data, config) => qs.stringify(data, config),
}
```
##### 请求示例2 实例封装
使用闭包缓存不同baseUrl请求体实例
```js
import axios from 'axios';
import store from '@/store';
import message from '@/utils/message';

const globalConfig = window.ENC;
const axiosInstance = (baseUrl) => {
    let instance = null;
    return () => {
        if (!instance) {
            instance = axios.create({
                baseURL: baseUrl,
                headers: { 'Content-Type': 'application/json' }, // x-www-form-urlencoded
                timeout: 25000,
            });
        }
        // 调度后端和获取调度流程增加验证
        if (baseUrl === globalConfig.base_url || baseUrl === globalConfig.base_workorder_url) {
            instance.interceptors.request.use((config) => {
                if (store.state.user.token || localStorage.getItem('token')) {
                    // eslint-disable-next-line no-param-reassign
                    config.headers.auth = store.state.user.token || localStorage.getItem('token');
                }
                return config;
            }, error => Promise.reject(error));

            instance.interceptors.response.use(response => response, (error) => {
                if (error.response && error.response.status === 401) {
                    message('error', '您的登录验证已过期，请重新登录');
                } else if (error.response && error.response.status === 403) {
                    message('error', '无权限访问');
                }
                return Promise.reject(error);
            });
        }
        return instance;
    }
};

export const axiosBaseInstance = axiosInstance(globalConfig.base_url)();
export const axiosPlanInstance = axiosInstance(globalConfig.base_plan_url)();
export const axiosConnectionInstance = axiosInstance(globalConfig.base_connection_url)();
export const axiosWorkOrderInstance = axiosInstance(globalConfig.base_workorder_url)();

```
