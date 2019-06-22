---
title: JAVA流实现文件批量打包下载
date: 2019-06-11 17:40:01
category: 工作干货
tag: 工作干货
---

话不多说，直接撸代码

``` java
	@ResponseBody
    public void downloadFiles(HttpServletRequest request, HttpServletResponse response, String[] filePaths) {
        if (filePaths == null || filePaths.length <= 0) {
            return ;
        }

        // 设置响应头
        response.reset();
        response.setCharacterEncoding("utf-8");
        response.setContentType("multipart/form-data");

        // 设置压缩包名称及不同浏览器中文乱码处理
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMddHHmmss");
        LocalDateTime localDateTime = LocalDateTime.now();
        String filename = "电子合同" + formatter.format(localDateTime) + ".zip";

        String agent = request.getHeader("AGENT");
        try {
            if (agent.contains("MSIE") || agent.contains("Trident")) {
                    filename = URLEncoder.encode(filename, "UTF-8");
            } else {
                filename = new String(filename.getBytes("UTF-8"), "ISO-8859-1");
            }
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }

        response.setHeader("Content-Disposition", "attachment;filename=\"" + filename  + "\""); // key不区分大小写
        
        try {
            // 设置压缩流，直接写入response,实现边压缩边下载
            ZipOutputStream zipOutputStream = new ZipOutputStream(new BufferedOutputStream(response.getOutputStream()));
            zipOutputStream.setMethod(ZipOutputStream.DEFLATED);

            DataOutputStream dataOutputStream = null;
            for (String filePath : filePaths) {
                String subFilename = formatter.format(LocalDateTime.now()) + filePath.substring(filePath.lastIndexOf("/") + 1);
                zipOutputStream.putNextEntry(new ZipEntry(subFilename));

                dataOutputStream = new DataOutputStream(zipOutputStream);
                BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream(new File(filePath)));
                byte[] buf = new byte[8192];
                int length = 0;

                while ((length = bufferedInputStream.read(buf)) != -1) {
                    dataOutputStream.write(buf, 0 , length);
                }
                dataOutputStream.flush();
                // dataOutputStream.close(); 若在此关闭，对应资源zipOutputStream也将关闭，则压缩包内仅有一个文件
                bufferedInputStream.close();
                zipOutputStream.closeEntry();
            }

            dataOutputStream.flush();
            dataOutputStream.close();
            zipOutputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```