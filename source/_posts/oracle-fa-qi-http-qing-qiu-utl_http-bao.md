---
title: 'Oracle发起HTTP请求——UTL_HTTP包'
date: 2018-08-26 21:16:19
categories:
- 数据库
tags:
- Oracle
- HTTP请求
---

![Oracle](/post-images/oracle-de-wen-jian-cao-zuo-util_file-bao-he-dbms_lob-bao.jpg)

无论在前端还是在后端，发送 HTTP 请求都是一件稀松平常的事。

前端可以用 jQuery 的 Ajax 方法 post、get 等，后端语言也都有 HTTP 请求相关的库可以直接调用。

当接到使用 Oracle 发起 HTTP 请求的需求时，我是拒绝的。

但是不小心一查，还真的支持，Oracle 自带的 UTL_HTTP 包真的可以发起 HTTP 请求。

<!-- more -->

```sql
create or replace function fnHTTP_REQUEST(param varchar2,content varchar2) return varchar2 is
  l_request    utl_http.req;
  l_response  utl_http.resp;
  l_result        varchar2(32767);
  l_url             varchar2(4000);
  l_param       varchar2(4000);
begin
  begin
    utl_http.set_response_error_check(false);
    utl_http.set_body_charset('UTF-8');
    --设置请求的地址
    l_url:='http://127.0.0.1:8080/api/api_test.jsp';
    --设置请求的路径
    l_param:='param='||param||'&content='||UTL_URL.escape(content);
    --设置用POST方式请求
    l_request:=utl_http.begin_request(l_url,'POST');
    --设置请求头部
    utl_http.set_header(l_request,'Content-Type','application/x-www-form-urlencoded');
    UTL_HTTP.SET_HEADER(l_request,'Content-Length',LENGTHB(l_param));
    --raw方式写入参数，可以避免中文变乱码
    UTL_HTTP.WRITE_RAW (l_request,UTL_RAW.CAST_TO_RAW(l_param));
    --发送请求并获取返回结果
    l_response := utl_http.get_response(l_request);
    if l_response.status_code = 200 then
      utl_http.read_text(l_response, l_result, length(l_result));
      utl_http.end_response(l_response);
    else--错误处理
      l_result := '网络访问错误！';
      utl_http.end_response(l_response);
    end if;
  exception--异常处理
    when others then
      dbms_output.put_line(sqlerrm);
      dbms_output.put_line(dbms_utility.format_error_backtrace);
      l_result := sqlerrm;
      if l_response.status_code is not null then
        utl_http.end_response(l_response);
      end if;
  end;
  return l_result;
end;
```
