---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python
  - java

toc_footers:
  - <a href='https://huoju.io'>Huoju Pro</a>

includes:
  - rest_general
  - rest_auth
  - rest_pub
  - rest_pub_assets
  - rest_pub_products
  - rest_pub_ticker
  - rest_pub_bar
  - rest_pub_depth
  - rest_pub_trades
  - rest_act
  - rest_act_info
  - rest_act_fee
  - rest_prv_bal
  - rest_prv_wal
  - rest_prv_wal_deposit
  - rest_prv_wal_txes
  - rest_prv_order
  - rest_prv_order_new
  - rest_prv_order_cancel
  - rest_prv_order_cancel_all
  - rest_prv_order_new_batch
  - rest_prv_order_cancel_batch
  - rest_prv_order_query
  - rest_prv_order_open
  - rest_prv_order_hist_curr
  - rest_prv_order_hist_v2
  - ws_general
  - ws_keep_alive
  - ws_auth
  - ws_sub
  - ws_sub_level1
  - ws_sub_level2
  - ws_sub_trades
  - ws_sub_bar
  - ws_sub_order
  - ws_req
  - ws_req_level2
  - ws_req_order_place
  - ws_req_order_cancel
  - ws_req_order_cancel_all
  - ws_req_order_open
  - ws_req_trades
  - expr
  - error_code

header_navigators:
  - <a href="https://github.com/HuojuPro/huoju-official-api-docs" class="current">Trading APIs</a>

search: true
---


# Huoju Pro API Documentation

Using Huoju Pro API

The Huoju Pro API is based on RESTful and WebSocket.

    The base URL for the RESTful API is https://huoju.co/.
    The URL for the WebSocket is: https://huoju.co/0/api/pro/v1/stream.

The RESTful API has JSON-encoded responses. All websocket messages are JSON-encoded.


## Demo codes

We provide comprehensive demos (currently available in python). We provide two types of demo codes:

* Short, self contained demo scripts that you can plugin to you larger system. 
* Large, complex demo scripts to show you how to design a trading strategy using APIs from this document.

See [https://github.com/HuojuPro/huoju-api-demo](https://github.com/HuojuPro/huoju-api-demo) for more details.
