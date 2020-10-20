# 如何在Android中执行耗时操作

- AsyncTask（抽象类）：简单来说，使用 AsyncTask 就是在 doInBackground() 方法中去执行具体的耗时任务，在 onProgressUpdate() 方法中进行 UI 操作，在 onPostExecute() 方法中执行一些任务的收尾工作
- Handler
- Thread（创建线程会消耗资源）
- Executors