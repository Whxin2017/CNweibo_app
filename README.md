
# Weibo_app test 11111 2222

这是一款关于微博的应用，该应用包括用户、微博功能，以及完整的登陆和用户身份验证系统。

### 目前功能：

* 注册功能，用户设置用户名、邮箱密码来注册属于自己的微博号
* 登陆功能，用户通过邮箱密码登陆自己的微博号,包括管理员、普通用户
* 编辑用户，用户可修改自己微博号的个人信息（用户名、邮箱、密码）
* 激活账户，用户注册后服务器会为注册邮箱发送电子邮件用于账户激活
* 微博功能，用户可上传文字和图片发布微博，也可看到其他用户的微博
* 关注用户，用户之间可相互关注

### 截图：
<img src="/lib/show_1.png" width="700">
<img src="/lib/show_2.png" width="700">
<img src="/lib/show_3.png" width="700">
<img src="/lib/show_4.png" width="700">


### 安装：

rails -version 5.1.2   

download from github:

$git clone https://github.com/Whxin2017/CNweibo_app.git

```
To get started with the app,clone the repo and then install the needed gems:  

$ bundle install --without production  

Next,migrate the database:  

$ rails db:migrate  

Finally,run the test suite to Verify tha everything is working correctly:  

$ rails test  

If the test suite passes,you'll be ready to run the app in a local server:  

$ rails server  
```


在浏览器中输入`localhost:3000`访问主页

## 使用
1.管理员登陆：

账号： `whxin1994@gmail.com`
密码： `thmin1990`

2.普通用户注册登陆

## cloud9云部署
 
https://cnweibo-app-whxin1994.cs50.io

## 本地测试

本项目包含了控制器测试、模型测试、集成测试，测试文件位于/test目录下，一键运行所有测试`rails test`

```
Running via Spring preloader in process 3725
Run options: --seed 62717

# Running:

.........................................................

Finished in 3.137166s, 18.1693 runs/s, 84.4711 assertions/s.
57 runs, 265 assertions, 0 failures, 0 errors, 0 skips
```

### 控制器测试
以主页面的控制器为例，位于`test/controllers/static_pages_controller_test.rb`,首先生成一个@base_title对象，测试点击此控制器是否会跳转至主页，使用assert_select检测当前页面是否有此控制器。

```
class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  def setup
	  @base_title="Ruby on Rails Weibo App"
  end
  test "should get home" do
    get root_path
    assert_response :success
	assert_select "title","#{@base_title}"
  end

```


### 模型测试

以用户模型为例, 位于`test/models/user_test.rb`, 首先生成一个`@user`对象，然后`assert`用户是否有效，这里的调用`valid`方法会去检查你的模型中的相关的`validates`语句是否正确，若`@user.valid?`为false, 那么此`assert`会报错，代表`"should be valid"`这条测试没有通过, 单独运行此测试文件使用`rake test test/models/user_test.rb`

```
class UserTest < ActiveSupport::TestCase
	def setup
		@user=User.new(name:"Example User",email:"user@example.com",password:"onepiece",password_confirmation:"onepiece")
	end

	test "should be valid" do
		assert @user.valid?
	end

	...

end
```

### 集成测试

以用户登录为例，位于`test/integration/user_login_test.rb`，首先同样生成一个@user模型，这个@user的用户名和密码可以在`test/fixtures/users.yml`中指定, 然后我们用get方法到达登录页面（sessions_login_path），然后使用post方法提交这个@user的账号密码来登录，如果登录成功，当前应该会跳转至homes控制器下的index方法进行处理，`assert_redirected_to`能判断这个跳转过程是否发生，然后调用`follow_redirect！`来紧跟当前的跳转，用`assert_template`来判读跳转后的视图文件是否为`homes/index`, 最后在这个视图文件下做一些测试，比如判断这个视图下连接为root_path的个数等等（根据当前登录的角色不同，当前的页面链接会不同，比如admin用户就会有控制面板的链接rails_admin_path，而普通用户没有，因此可以根据链接的个数来判断当前登录用户的角色）

```
class UsersLoginTest < ActionDispatch::IntegrationTest
	def setup
		@user = users(:michael)
	end
test "login with valid information" do
		get login_path
		post login_path,params: { session: {email: @user.email,
								  password: 'password'}  }
		assert_redirected_to @user
		follow_redirect!
		assert_template 'users/show'
		assert_select "a[href=?]",login_path,count:0
		assert_select "a[href=?]",logout_path
		assert_select "a[href=?]",user_path(@user)
	end
```



