#ajaxFileUploa上传文件处理，默认只支持文件的上传。但通过修改其创建form的方法可以长传多个文件。
###上传方式主要通过fileElementId，也可以自己重新定义一个。
    $.ajaxFileUpload({
          url: url,
          method: 'post',
          fileElementId: "sealStreamFile digitalCertificateFile",
          fileElementName: 'archiveStreamFile',
          branches_data: data,
          dataType: 'text/html',
          success: function (data) {
          }
          ....
###修改其createUploadForm，获取 fileElementId或者自己定义的fileElementName。
    jQuery.createUploadForm = function (id, s) {
        var formId = 'jUploadForm' + id;
        var fileId = 'jUploadFile' + id;
        var form = $('<form  action="" method="POST" name="' + formId + '" id="' + formId + '" enctype="multipart/form-data"></form>');
        var filesId = *s.fileElementId*;
        var fileName = *s.fileElementName*;

        //根据上传文件id处理
        $.each(filesId.split(" "), function (i, id) {
            var oldElement = $('#' + id);
            var newElement = $(oldElement).clone();
            $(oldElement).attr('id', fileId);
            $(oldElement).before(newElement);
            $(oldElement).appendTo(form);
            dataFile.push(oldElement);
        });

        //根据上传文件Name处理
        var oldElementName = jQuery("input[name=" + fileName + "]");
        oldElementName.each(function () {
            var newElement = jQuery($(this)).clone();
            jQuery(this).attr('name', fileName);
            jQuery(this).before(newElement);
            jQuery(this).appendTo(form);
            dataFiles.push(jQuery(this));
        });

        var data = s.branches_data;
        if (data) {
            for (var name in data) {
                $(form).append('<input type="hidden" name="' + name + '" value="' + ( data[name] || " ") + '" >');
            }
        }
        $(form).css('position', 'absolute');
        $(form).css('top', '-1200px');
        $(form).css('left', '-1200px');
        $(form).appendTo('body');
        return form;
    };
#####在文件上传失败后，再次上传会出现input（type=file）的val为空的现象，因为已经把原来的input放到ajax创建的form里面了，而在原来input前面克隆一个和他一模一样的input,你感觉没有变化，其实以及换了一个新的input.
#####解决文件失败上传后，input有值问题。我个人使用的事放到缓存里面。创建dataFiles或者dataFile数组，把原来的input放到里面，失败在取出来，并且清空数组。
    dataFile.push(oldElement);
    dataFiles.push(jQuery(this));
 
