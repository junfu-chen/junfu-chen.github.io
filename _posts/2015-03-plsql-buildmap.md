---
layout: post
tumblr_id: 1121203094
disqus_comments: true
date: 2015-02-10 10:00:00 UTC+8
title: 在oracle:pl/sql中构建一个map 
menu: 数据库
---
{% highlight SQL%}     
/*字符串：[key:value]* 构建成一个MAP*/
FUNCTION BUILD_MAP(p_str IN VARCHAR2, p_delimiter IN VARCHAR2)
  RETURN ty_split_value_tbl IS
  /*
    使用前必需创建下面对象
  CREATE OR REPLACE TYPE ty_split_value_rec  as object (key varchar2(100),Value VARCHAR2 (4000));
  CREATE OR REPLACE TYPE ty_split_value_tbl IS TABLE OF ty_split_value_rec;*/
  j         INT := 0;
  i         INT := 1;
  len       INT := 0;
  len1      INT := 0;
  str       VARCHAR2(4000);
  str_split ty_split_value_tbl := ty_split_value_tbl();
  l_rec     ty_split_value_rec := ty_split_value_rec(null, null);
BEGIN
  len  := LENGTH(p_str);
  len1 := LENGTH(p_delimiter);
  WHILE j < len LOOP
    j := INSTR(p_str, p_delimiter, i);
    IF j = 0 THEN
      j           := len;
      str         := SUBSTR(p_str, i);
      l_rec.key   := LTRIM(RTRIM(SUBSTR(str, 0, INSTR(str, ':') - 1)));
      l_rec.Value := LTRIM(RTRIM(SUBSTR(str, INSTR(str, ':') + 1)));
      str_split.EXTEND;
      str_split(str_split.COUNT) := l_rec;
   
      IF i >= len THEN
        EXIT;
      END IF;
    ELSE
      str         := SUBSTR(p_str, i, j - i);
      i           := j + len1;
      l_rec.key   := LTRIM(RTRIM(SUBSTR(str, 0, INSTR(str, ':') - 1)));
      l_rec.Value := LTRIM(RTRIM(SUBSTR(str, INSTR(str, ':') + 1)));
      str_split.EXTEND;
      str_split(str_split.COUNT) := l_rec;
    END IF;
  END LOOP;

  RETURN str_split;
END BUILD_MAP;
{% endhighlight%}