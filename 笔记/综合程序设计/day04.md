# day04

---

需要注意的是，在前端返回的JSON变量会默认以小写形式返回后端

人脸识别后端报错返回：result的JSON对象为空，或JSON相关问题，则关注.html静态网页和controller层，关注.html向后端发送数据时的body绑定：
```java
<script>
loginFaceButton.addEventListener('click', async () => {  
    try {  
        // 获取表单数据  
        const imageBase64 = photo.src.split(',')[1];  
  
        // 简单验证  
        if (!imageBase64)  
        {  
            messageDiv.textContent = '请上传人脸照片';  
            return;  
        }  
  
        // 发送到后端  
        const response = await fetch('/user/loginFace', {  
            method: 'POST',  
            headers: {  
                'Content-Type': 'application/json'  
            },
            


  
            // 就是此处
            body: JSON.stringify({  
                imagetoken: imageBase64  
                // 前边的是传到后端后，后端接收的变量名，需使用dto层存储，后边的是html中定义的前端获取的变量
            })  





        });  
  
        if (response.ok) {  
            messageDiv.textContent = '用户登录成功！';  
            //跳转到登录界面login.html  
            window.location.href = '/home.html';  
  
        } else {  
            messageDiv.textContent = '登录失败: ' + response.statusText;  
        }  
  
    } catch (err) {  
        console.error('登录错误:', err);  
        messageDiv.textContent = '登录失败: ' + err.message;  
    }  
  
});
</script>
```