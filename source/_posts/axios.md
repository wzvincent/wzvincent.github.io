---
title: Axios
date: 2018-06-07 11:12:43
categories: 
	    - front end
tags: 
        - axios
        - promise
        - async
---
## GET METHOD
### Basic Example
```
    axios.get(url, params, config)
```
### Advance Example
```
    export const getOperationLog = params => {
        return axios.get(`${host}/imsi/listOptLog`, {params: params}).then(res => res.data)
    }
```
<!-- more -->
## POST METHOD
### Basic Example
```
    axios.post(url, params, config)
```
### Advance Example
#### form-data
```
    export const updateItem = params => {
        return axios.post(`${host}/imsi/updateItem`, 
               params, 
               {headers: {'Content-Type': 'multipart/form-data'}
               })
               .then(res => res.data)
    }
```
#### x-www-form-urlencoded
```
    export const getVirusManageList = params => {
        return axios.post(`${host}/anti/list`, 
               qs.stringify(params), 
               {headers: {'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8'}
               })
               .then(res => res.data)
    }
```
#### json
```
    export const updateItem = params => {
        return axios.post(`${host}/anti/update`, 
        params)
        .then(res => res.data)
    }
```

&nbsp;
&nbsp;
*To be continued...*
