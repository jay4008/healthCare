import axios from 'axios';
// import store from '../../store';
// import Config from 'react-native-config';
import AsyncStorage from '@react-native-async-storage/async-storage';
import RNFetchBlob from 'rn-fetch-blob';
import { baseUrl } from './MyUtility';
// import { setApiErrorTrue } from '../../store/Welcome/apiError';
let  BASE_URL = baseUrl;
// let  BASE_URL_IMAGE = Config.NODEBB_URL;


('Bearer 961797e3-42d8-4646-a8bb-b50b580e7282');

const API_ERROR_CODES = [500, 501, 502, 503, 504, 511]
class GstHelper {
  API_TIME_OUT = 60000;
  /**
   *
   * Get Api Calls
   */

  async get(relativeUrl) {
    const tokenValue = await AsyncStorage.getItem('token');
    return new Promise((resolve, reject) => {
      console.log('tokenValue getToken **', tokenValue);
      const headers = {};
      if (tokenValue) {
        //   const headers = {
        //     Authorization: tokenValue //'Bearer ' + store.getState().Register.token,
        //  };
        headers.Authorization = tokenValue;
      }
      console.log('tokenValue headers **', headers);
      const URL = `${BASE_URL}${relativeUrl}`;
      console.log('getURL =---->', URL);
      axios({
        method: 'get',
        url: URL,
        headers: headers,
        timeout: this.API_TIME_OUT,
      })
        .then((sucessRes) => {
          console.log('data  from api', URL, sucessRes);
          console.log('data  time', new Date());
          resolve(sucessRes);
        })
        .catch((errorRes) => {
          console.log('error res==>', errorRes.response);
          reject(errorRes);
        });
    });
  }

  /**
   *
   * Post api Calls
   */
  post(relativeUrl, body) {
    console.log('relativeUrl body api =---->', relativeUrl, body);
    return new Promise((resolve, reject) => {
      const headers = {
        // Authorization: 'Bearer ' + store.getState().Register.token,
        // currentRole: 'Explorer',
      };
      const URL = `${BASE_URL}${relativeUrl}`;
      console.log('URL =---->', URL, body);
      axios({
        method: 'post',
        url: URL,
        data: body,
        // headers: headers,
        timeout: this.API_TIME_OUT,
      })
        .then((sucessRes) => {
          console.log('response', sucessRes);
          resolve(sucessRes);
        })
        .catch((errorRes) => {
          console.log('err', errorRes, relativeUrl, body);
          reject(errorRes);
        });
    });
  }

  put(relativeUrl, body) {
    console.log('relativeUrl body api =---->', relativeUrl, body);
    return new Promise((resolve, reject) => {
      const headers = {
        // Authorization: 'Bearer ' + store.getState().Register.token,
        // currentRole: 'Explorer',
      };
      const URL = `${BASE_URL}${relativeUrl}`;
      console.log('URL =---->', URL, body);
      axios({
        method: 'put',
        url: URL,
        data: body,
        // headers: headers,
        timeout: this.API_TIME_OUT,
      })
        .then((sucessRes) => {
          console.log('response', sucessRes);
          resolve(sucessRes);
        })
        .catch((errorRes) => {
          console.log('err', errorRes, relativeUrl, body);
          reject(errorRes);
        });
    });
  }

  delete(relativeUrl) {
    console.log('relativeUrl body api =---->', relativeUrl);
    return new Promise((resolve, reject) => {
      const headers = {
        // Authorization: 'Bearer ' + store.getState().Register.token,
        // currentRole: 'Explorer',
      };
      const URL = `${BASE_URL}${relativeUrl}`;
      console.log('URL =---->', URL);
      axios({
        method: 'delete',
        url: URL,
        // data: body,
        // headers: headers,
        timeout: this.API_TIME_OUT,
      })
        .then((sucessRes) => {
          console.log('response', sucessRes);
          resolve(sucessRes);
        })
        .catch((errorRes) => {
          console.log('err', errorRes, relativeUrl, body);
          reject(errorRes);
        });
    });
  }

  deleteImage(relativeUrl) {
    console.log('relativeUrl body api =---->', relativeUrl);
    return new Promise((resolve, reject) => {
      const headers = {
      };
      const URL = `${BASE_URL}${relativeUrl}`;
      console.log('URL =---->', URL);
      axios({
        method: 'delete',
        url: URL,
        headers: headers,
        timeout: this.API_TIME_OUT,
      })
        .then((sucessRes) => {
          console.log('response', sucessRes);
          resolve(sucessRes);
        })
        .catch((errorRes) => {
          // console.log('err', errorRes, relativeUrl, body);
          reject(errorRes);
        });
    });
  }

  formDatapost(relativeUrl,data) {
    return new Promise((resolve, reject) => {
      const headers = {
        'Content-Type': 'multipart/form-data',
      };
      const URL = `${BASE_URL}${relativeUrl}`;
      console.log('formDatapost url----------->', URL);
      RNFetchBlob.fetch('POST', URL, headers, data)
        .then((sucessRes) => {
          resolve(sucessRes);
        })
        .catch((errorRes) => {
          reject(errorRes);
        });
    });
  }
}

///// *************** refresh token part ***************/////////////
let isRefreshing = false;
let failedQueue = [];

const processQueue = (error, token = null) => {
  failedQueue.forEach((prom) => {
    if (error) {
      prom.reject(error);
    } else {
      prom.resolve(token);
    }
  });

  failedQueue = [];
};

axios.interceptors.response.use(
  function (response) {
    return response;
  },
  function (error) {
    console.log('axios.interceptors error block **', error.response);
    if (error && error.response && error.response.status == 401) {
      const originalRequest = error.response.config;

      if (!originalRequest._retry) {
        if (isRefreshing) {
          return new Promise(function (resolve, reject) {
            failedQueue.push({ resolve, reject });
          })
            .then((token) => {
              originalRequest.headers['Authorization'] = token;
              return axios(originalRequest);
            })
            .catch((err) => {
              return Promise.reject(err);
            });
        }

        originalRequest._retry = true;
        isRefreshing = true;

        return new Promise(function (resolve, reject) {
          //  _retrieveData('key')
          //     .then(async (key) => {

          const headerValue = error.response.config.headers;
          const headerVal = headerValue.Authorization;
          let addBererHeader = 'Bearer ' + headerVal;
          const headers = {
            Authorization: addBererHeader,
          };
          let subUrls = 'refreshtoken';
          const URL = `${BASE_URL}${subUrls}`;

          axios({
            method: 'get',
            url: URL,
            headers: headers,
            timeout: 60000,
          })
            .then(async (tokenRefreshResponse) => {
              const token = tokenRefreshResponse.data;
              if (typeof token === 'string' || token instanceof String) {
                await AsyncStorage.setItem('tokenValue', token);
              }
              // store.dispatch({
              //   type: 'VERIFY_SUCCESS',
              //   token,
              //   key,
              // });
              error.response.config.headers.Authorization = token;
              const originalRequest = error.response.config;
              processQueue(null, token);
              resolve(axios(originalRequest));
            })
            //})
            .catch((err) => {
              console.log('interceptors refreshtoken block ** ', err.response);
              processQueue(err, null);
              reject(err);
            })
            .finally(() => {
              isRefreshing = false;
            });
        });
      }
    } else if (error && error.response && API_ERROR_CODES.includes(error.response.status)) {
      const originalRequest = error.response.config;
    //   store.dispatch(setApiErrorTrue(originalRequest));
    } else {
      return Promise.reject(error);
    }
  },
);

export const GStHelper = new GstHelper();
