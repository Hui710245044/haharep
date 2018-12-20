/**
 * Created by RondaFul on 2016/11/23.
 */
import {http} from '../lib/http-plus';

export const url_store_list = "get|warehouse"
export const store_list = function (data) {
    return http(url_store_list, data);
};
export const url_store_check = "get|warehouse/:id"
export const store_check = function (id) {
    return http(Url2(url_store_check, {id: id}));
};
export const url_store_add = "post|warehouse"
export const store_add = function (data) {
    return http(url_store_add, data);
};
export const url_store_status = "post|warehouse/status"
export const store_status = function (data) {
    return http(url_store_status, data);
};
export const url_store_update = "post|warehouse/updateWareHouse"
export const store_update = function (data) {
    return http(url_store_update, data);
};
export const url_store_getCarrier = "get|warehouse/getCarrier"
export const store_getCarrier = function () {
    return http(url_store_getCarrier);
};
export const url_store_getShip = "get|warehouse/getShip"
export const store_getShip = function (data) {
    return http(url_store_getShip, data);
};
export const url_store_bound = "put|warehouse/:id"
export const store_bound = function (id, data) {
    return http(Url2(url_store_bound, {id: id}), data);
};
export const url_store_type = "get|warehouse/getWarehouseType"
export const store_type = function () {
    return http(url_store_type);
};
export const url_store_type_two = "get|warehouse/getWarehousesByType?type=:id"
export const store_type_two = function (id) {
    return http(Url2(url_store_type_two, {id: id}));
};
export const url_store_address = "get|warehouse/getArea"
export const store_address = function () {
    return http(url_store_address);
};
/*---删除*/
export const url_store_del = "delete|warehouse/:id"
export const store_del = function (id) {
    return http(Url2(url_store_del, {id: id}));
};
// 提交仓库系统配置
export const url_warehouse_config = "post|warehouse/config/:id";
export const api_warehouse_config = function (id, data) {
    return http(Url2(url_warehouse_config, {id: id}), data);
};