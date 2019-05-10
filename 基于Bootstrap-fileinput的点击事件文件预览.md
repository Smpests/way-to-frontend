### 基于Bootstrap-fileinput插件的文件预览 ###
1.使用方法: 复制粘贴至js文件, 点击事件调用该方法$.fn.filePreviewer即可
2.前提: 页面中有经Bootstrap-fileinput渲染过的input标签
3.注意: 后台返回数据类型为byte, 并在response中设置'Content-Type', 值为文件的mimetype;设置'Content-disposition', 值为'attachment;filename=yourfile' 
4.附Bootstrap-fileinput下载地址: [https://github.com/kartik-v/bootstrap-fileinput](https://github.com/kartik-v/bootstrap-fileinput)
5.代码如下
```
    /**
     * Author: Smpests
     * Date: 2019/5/8
     * Extension: Bootstrap-fileinput插件的仅文件预览接口
     */
	// url example: /test?file=55   (即定位到55号文件，能确认文件位置的参数)
	// 参数id: fileinput对象的载体input标签的id属性值,
    $.fn.filePreviewer = function(id, url) {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', url, true);        // 也可以使用POST方式，根据接口
        xhr.responseType = "blob";    // 返回类型blob
        xhr.onload = function() {
            if (this.status === 200) {
                var blob = this.response;
                var fileinput = $('#' + id).data('fileinput'),
                    $zone = fileinput.$dropZone;
                blob.lastModifiedDate = new Date();
                blob.lastModified = new Date().getTime();
                blob.name = this.getResponseHeader('Content-disposition').split(';')[1].split('=')[1];
                fileinput.readFiles([blob]);
                fileinput._setFileDropZoneTitle();
                setTimeout(function() {
                    var $frame = $zone.find(".table .file-preview-thumbnails .kv-preview-thumb");
                    fileinput.zoom($frame.attr('id'));
                    var $btClose = $('#kvFileinputModal .modal-content .modal-header .kv-zoom-actions .btn-close');
                    $btClose.click(function () {
                        fileinput.clear();
                    });
                }, 100);
            }
        };
        xhr.send();
    };
```
6.后台示例, 使用Python/Flask/
```
@bp.route('/resources/preview', methods=['GET'])
def resources_preview():
    file_id = request.args.get('id')
    db = get_db()
    cursor = db.cursor()
    try:
        sql = "SELECT * FROM %s WHERE fileID = %s"
        cursor.execute(sql % ('resources', file_id))
        result = cursor.fetchone()
        filepath = result[2]
        filename = result[3]

        # 流式读取, 可将该方法移至别处调用
        def send_file(path):
            with open(path, 'rb') as f:
                while 1:
                    data = f.read(1024 * 1024)  # 每次读取1M
                    if not data:
                        break
                    yield data
        # response = make_response(send_from_directory(file_path, filename, as_attachment=True))
        response = make_response()
        response.response = send_file(filepath)
        response.headers["Content-Type"] = mimetypes.guess_type(filepath)[0]
        response.headers["Content-disposition"] = 'attachment;filename={0}'.format(filename)  # 如果不加上这行代码，导致下图的问题
    except Exception as e:
        raise e
    return response
```
7.效果图(非国内图床)
![](https://i.imgur.com/NDYQRJX.png)