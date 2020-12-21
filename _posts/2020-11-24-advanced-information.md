---
title: Advanced Features
description: 5
---

<ol type="1">
  <li><h3>Navigation changing:</h3></li>
  <p>With the feature MaterialPageRoute we can change our screens if they are all based on Stateful Widgets.</p>
  
<p><strong>1. Let's create a new async function to navigate our previously built HwID screen.</strong></p>
<pre><div id="copy-button33" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> _navigateHWID() async {
	Navigator.push(
	context, MaterialPageRoute(builder: (context) => AuthScreen()));
}
<span class="pln">
</span></code></pre>

<p><strong>2. Let's create another fully formed classical login form named Email.</strong></p>
<pre><div id="copy-button34" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  _navigateEmail() async {
	Navigator.push(
	context, MaterialPageRoute(builder: (context) => EmailScreen()));
}
<span class="pln">
</span></code></pre>

<p><strong>3. Under our Welcome Screen State lets call our routes.</strong></p>
<pre><div id="copy-button34" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  @override
	Widget build(BuildContext context) {
	return Scaffold(
	backgroundColor: Colors.transparent,
	appBar: AppBar(
	title: Text('Welcome'),
	),
	body: Container(
	width: MediaQuery.of(context).size.width,
	decoration: BoxDecoration(
	gradient: LinearGradient(
	colors: [Colors.white10, Colors.black],
	begin: Alignment.topCenter,
	end: Alignment.bottomCenter)),
	child: Column(
	mainAxisAlignment: MainAxisAlignment.start,
	children: <Widget>[
		Column(
		crossAxisAlignment: CrossAxisAlignment.stretch,
		textBaseline: TextBaseline.ideographic,
		children: [
		Container(
		padding: EdgeInsets.all(5.0),
		child: Text(
		'Continue to your sign in process with:',
		textAlign: TextAlign.justify,
		style: TextStyle(fontSize: 17.0, color: Colors.amber),
		),
		),
		authRichButton("Simple Build", " - Huawei ID", _navigateHWID),
		authRichButton(
		"Complex Build", " - Email + Huawei ID", _navigateEmail),
		])
		],
		),
		),
		);
	}
</Widget>
</code>
</pre>

<p><strong>4. Under our main.dart let us call our Welcome Screen as our main.</strong></p>
<pre><div id="copy-button34" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>class _MyAppState extends State<MyApp> {
	@override
	Widget build(BuildContext context) {
	return MaterialApp(
	title: 'Huawei Auth Service',
	theme: ThemeData(),
	darkTheme:ThemeData.dark(),
	home: WelcomeScreen(),
	);
}
}
</MyApp>
</code>
</pre>

<li><h3>Complex Build and Runnable:</h3></li>
<p>AWithin this complex build we will investigate building the classical complex form of login and registries followed by login with Huaawei ID Authorization Button.</p>

<p><strong>1. Starting with our util let's started building Shared Preferences.</strong></p>
<pre><div id="copy-button35" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  //Under pubsec.yaml
dependencies:
	shared_preferences: ^0.5.6

//Under util/shared_prefs.dart
import 'package:shared_preferences/shared_preferences.dart';

class SharedPrefs {
  static SharedPreferences _sharedPrefs;
  init() async {
    if (_sharedPrefs == null) {
      _sharedPrefs = await SharedPreferences.getInstance();
    }
  }
  bool getBool(String sharedPrefParam) {
    return _sharedPrefs.getBool(sharedPrefParam);
  }
  void setBool(String sharedPrefParam, bool value) {
    _sharedPrefs.setBool(sharedPrefParam, value);
  }
  int getInt(String sharedPrefParam) {
    return _sharedPrefs.getInt(sharedPrefParam);
  }
  void setInt(String sharedPrefParam, int value) {
    _sharedPrefs.setInt(sharedPrefParam, value);
  }

  String getString(String sharedPrefParam) {
    return _sharedPrefs.getString(sharedPrefParam);
  }
  void setString(String sharedPrefParam, String value) {
    _sharedPrefs.setString(sharedPrefParam, value);
  }
}

final sharedPrefs = SharedPrefs();
<span class="pln">
</span></code></pre>

<p><strong>3. Validator for our email fields.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>Validator {
	static bool isValidEmail(String str) {
	return RegExp(
	r'^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$')
	.hasMatch(str);
}
}
        <span class="pln">
</span></code></pre>

<aside class="special">
  <p><strong>Note: Everything from Point4 to Point11 are under the State of EmailScreen:</strong></p>
</aside>

<p><strong>4. Implement the Global UI controllers and states.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>bool isLoginPage;
	bool isLoading;

	final _signInFormKey = GlobalKey();
	TextEditingController signInEmail = TextEditingController();
	TextEditingController signInPassword = TextEditingController();

	final _signUpFormKey = GlobalKey<FormState>();</FormState>
	TextEditingController signUpName = TextEditingController();
	TextEditingController signUpEmail = TextEditingController();
	TextEditingController signUpPassword = TextEditingController();
</code></pre>

<p><strong>5. Implement the initial state.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>@override
	void initState() {
	super.initState();
	isLoading = false;
	isLoginPage = true;
}
</code></pre>

<p><strong>6. Implement the sign in as async function.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>_signIn() async {
    // BUILD DESIRED PARAMS
    AuthParamHelper authParamHelper = new AuthParamHelper();
    authParamHelper
      ..setIdToken()
      ..setAuthorizationCode()
      ..setAccessToken()
      ..setProfile()
      ..setEmail()
      ..setId()
      ..addToScopeList([Scope.openId])..setRequestCode(8888);
    // GET ACCOUNT INFO FROM PLUGIN
    try {
      final AuthHuaweiId accountInfo = await HmsAccount.signIn(authParamHelper);
      setState(() {

      });
    } on Exception catch(exception) {
      print(exception.toString());
    }
    /// TO VERIFY ID TOKEN, AuthParamHelper()..setIdToken()
    //performServerVerification(accountInfo.idToken);
  }
</code></pre>

<p><strong>7. Implement the handler or request handler.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>requestSignUpHandler(response) {
	if (response != null && response.statusCode == 200) {
	var json = jsonDecode(response.body);
	if (json['result'] == true) {
	loginProcedure(json['token']);
}
else {
_alreadyTakenEmailError();
}
}
else {
// another error occured
_someErrorHappenedAlert();
}
}
</code></pre>

<p><strong>8. Implement the login procedure.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>void loginProcedure(token) {
	sharedPrefs.setString("token", token);
	Navigator.push(
	context,
	MaterialPageRoute(
	builder: (context) => WelcomeScreen()));
}
</code></pre>

<p><strong>9. Implement the error informer.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> Future<void> _someErrorHappenedAlert() async {
    setState(() {
      isLoading = !isLoading;
    });
    return showDialog<void>(
      context: context,
      barrierDismissible: false, // user must tap button!
      builder: (BuildContext context) {
        return AlertDialog(
          title: Text('Something Went Wrong!', style: TextStyle(
            fontSize: 16,
          )),
          content: SingleChildScrollView(
            child: ListBody(
              children: <Widget>[
                Text('An error occurred during request.'),
                Text('Please check your connection.'),
              ],
            ),
          ),
          actionsPadding: EdgeInsets.symmetric(horizontal: 30),
          actions: <Widget>[
            FlatButton(
              child: Text('OK', style: TextStyle(color: Colors.blue),),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }
</Widget></Widget></void></void>
</code></pre>

<p><strong>10. Implement error state if email is already taken.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> Future<void> _alreadyTakenEmailError() async {
    setState(() {
      isLoading = !isLoading;
    });
    return showDialog<void>(
      context: context,
      barrierDismissible: false, // user must tap button!
      builder: (BuildContext context) {
        return AlertDialog(
          title: Text('Email Address is in Use!', style: TextStyle(
            fontSize: 16,
          )),
          content: SingleChildScrollView(
            child: ListBody(
              children: <Widget>[
                Text('Please enter another email address.'),
              ],
            ),
          ),
          actionsPadding: EdgeInsets.symmetric(horizontal: 30),
          actions: <Widget>[
            FlatButton(
              child: Text(
                'Change Address', style: TextStyle(color: Colors.blue),),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }
</Widget></Widget></void></void>
</code></pre>

<p><strong>11. Implement the error state that will inform if authetication encounters some error.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>Future<void> _authenticationError() async {
    setState(() {
      isLoading = !isLoading;
    });
    return showDialog<void>(
      context: context,
      barrierDismissible: false, // user must tap button!
      builder: (BuildContext context) {
        return AlertDialog(
          title: Text('Authentication Failed', style: TextStyle(
            fontSize: 16,
          )),
          content: SingleChildScrollView(
            child: ListBody(
              children: <Widget>[
                Text('Please check your email and password.'),
              ],
            ),
          ),
          actionsPadding: EdgeInsets.symmetric(horizontal: 30),
          actions: <Widget>[
            FlatButton(
              child: Text('OK', style: TextStyle(color: Colors.blue),),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }
</Widget></Widget></void></void>
</code></pre>

<li><h3>UI Building</h3></li>
<p>Now that we have completed functionality we'll now build UI for our functions and screens.</p>

<p><strong>1. Let us implement our divider.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>// Divider
	@override
	Widget build(BuildContext context) {
	var divider = Container(
	margin: EdgeInsets.symmetric(vertical: 6),
	child: Row(children: <Widget>[
		Expanded(
		child: new Container(
		margin: const EdgeInsets.only(left: 10.0,right: 20.0),
		child: Divider(
		color: Colors.white24,
		height: 36,
		thickness: 0.7
		)),
		),
		Text("or", style: TextStyle(color: Colors.white24, fontSize: 24),),
		Expanded(
		child: new Container(
		margin: const EdgeInsets.only(left: 20.0,right: 10.0),
		child: Divider(
		color: Colors.white24,
		height: 36,
		thickness: 0.7
		)),
		)
		]),
		);
		//...sign in
		//...sign up
	}
</Widget>
</code></pre>

<p><strong>2. Let us implement our sign up.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>@override
	Widget build(BuildContext context) {
	//...divider

	var signInPage = Container(
      alignment: Alignment.center,
      margin: EdgeInsets.symmetric(horizontal: 30, vertical: 0),
      child: Column(
        mainAxisSize: MainAxisSize.max,
        mainAxisAlignment: MainAxisAlignment.center,
        crossAxisAlignment: CrossAxisAlignment.stretch,
        children: [
          Expanded(
            child: SingleChildScrollView(
              child: AbsorbPointer(
                absorbing: isLoading,
                child: Form(
                  key: _signInFormKey,
                  child: Column(
                      children: <Widget> [
                        SizedBox(height: 50),
                        Text("Flutter Log In Reference", style: TextStyle(
                            fontSize: 30,
                            color: Colors.amber
                        ),),
                        SizedBox(height: 60),
                        TextFormField(
                          textCapitalization: TextCapitalization.words,
                          textAlignVertical: TextAlignVertical.center,
                          textAlign: TextAlign.left,
                          controller: signInEmail,
                          obscureText: false,
                          autofocus: false,
                          maxLines: 1,
                          autocorrect: true,
                          textInputAction: TextInputAction.next,
                          keyboardType: TextInputType.emailAddress,
                          validator: (input) {
                            if(Validator.isValidEmail(input.toString())) {
                              return null;
                            } else {
                              return "Please enter a valid email.";
                            }
                          },
                          onFieldSubmitted: (_) => FocusScope.of(context).nextFocus(),
                          cursorColor: Colors.amber,
                          style: TextStyle(fontSize: 16, color: Colors.white,),
                          decoration: InputDecoration(
                              enabled: true,
                              prefixIcon: Icon(Icons.email, color: Colors.amber,size: 18,),
                              focusColor: Colors.amber,
                              contentPadding: EdgeInsets.all(10),
                              border: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              enabledBorder: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              hintText: "Email Address",
                              hintStyle: TextStyle(color: Colors.white54)
                          ),
                        ),
                        SizedBox(height: 30,),
                        TextFormField(
                          textCapitalization: TextCapitalization.words,
                          textAlignVertical: TextAlignVertical.center,
                          textAlign: TextAlign.left,
                          controller: signInPassword,
                          obscureText: true,
                          autofocus: false,
                          maxLines: 1,
                          autocorrect: false,
                          textInputAction: TextInputAction.next,
                          keyboardType: TextInputType.text,
                          validator: (input) {
                            if(input.toString().length >= 6) {
                              return null;
                            } else {
                              return "Please enter your password.";
                            }
                          },
                          cursorColor: Colors.amber,
                          style: TextStyle(fontSize: 16, color: Colors.white),
                          decoration: InputDecoration(
                              enabled: true,
                              prefixIcon: Icon(Icons.lock, color: Colors.amber,size: 18),
                              focusColor: Colors.amber,
                              contentPadding: EdgeInsets.all(10),
                              border: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              enabledBorder: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              hintText: "Password",
                              hintStyle: TextStyle(color: Colors.white54)
                          ),
                        ),
                        Container(
                            margin: EdgeInsets.only(bottom: 12),
                            child: Row(
                              mainAxisAlignment: MainAxisAlignment.center,
                              children: [
                                InkWell(
                                  onTap: () => {
                                   // Navigator.pushNamed(context, ForgotPasswordPage.route),
                                  },
                                  splashColor: Colors.amber,
                                  child: Container(
                                    margin: EdgeInsets.symmetric(vertical: 12),
                                    padding: EdgeInsets.symmetric(vertical: 5, horizontal: 5),
                                    child: Text('Forgot Password', style: TextStyle(
                                      color: Colors.white70,
                                      fontSize: 13,
                                    )),
                                  ),
                                )
                              ],
                            )
                        ),
                        Container(
                          height: 36,
                          margin: EdgeInsets.symmetric(horizontal: 0, vertical: 4),
                          child: FlatButton(
                            shape: RoundedRectangleBorder(
                              borderRadius: BorderRadius.circular(10.0),
                              //side: BorderSide(color: Colors.red)
                            ),
                            child: Row(
                              mainAxisAlignment: MainAxisAlignment.spaceAround,
                              children: [
                                isLoading ? SizedBox() : Icon(Icons.email, color: Colors.white70.withOpacity(0.4), size: 22),
                                isLoading ? SizedBox(height:22, width:22,child: CircularProgressIndicator(strokeWidth: 4,)) : Text('Sign in with Email', style: TextStyle(
                                  color: Colors.white70,
                                  fontSize: 16,
                                ),),
                                isLoading ? SizedBox() : SizedBox(width: 26,),
                              ],
                            ),
                            color: Colors.white12,
                            focusColor: Colors.amber,
                            splashColor: Colors.amber,
                            onPressed: () {
                              if (_signInFormKey.currentState.validate()) {
                                setState(() {
                                  isLoading = !isLoading;
                                });
                                //requestSignIn().then((response) => (requestSignInHandler(response)));
                              }
                            },
                          ),
                        ),
                        divider,
                        Container(
                          margin: EdgeInsets.symmetric(horizontal: 0, vertical: 4),
                          height: 36,
                          child: FlatButton(
                            shape: RoundedRectangleBorder(
                              borderRadius: BorderRadius.circular(10.0),
                              //side: BorderSide(color: Colors.red)
                            ),
                            child: Row(
                              mainAxisAlignment: MainAxisAlignment.spaceAround,
                              children: [
                                Image.asset('assets/images/hw_24x24_logo.png', width: 24, height: 24),
                                Text('Sign in with HUAWEI ID', style: TextStyle(
                                  color: Colors.white,
                                  fontSize: 16,
                                )),
                                SizedBox(width: 20,),
                              ],
                            ),
                            color: Color(0xffef484b),
                            focusColor: Colors.amber,
                            splashColor: Colors.amber,
                            onPressed: () {
                              _signIn();
                            },
                          ),
                        ),
                        Container(
                          margin: EdgeInsets.symmetric(horizontal: 10, vertical: 24),
                          child: Row(
                            mainAxisAlignment: MainAxisAlignment.center,
                            children: [
                              Text('Don\'t have an account yet?', style: TextStyle(
                                color: Colors.white70,
                                fontSize: 14,
                              ),),
                              InkWell(
                                onTap: () => {
                                  setState(() {
                                    isLoginPage = !isLoginPage;
                                  })
                                },
                                focusColor: Colors.amber,
                                splashColor: Colors.amber,
                                child: Container(
                                  padding: EdgeInsets.symmetric(vertical: 5, horizontal: 5),
                                  child: Text('Register', style: TextStyle(
                                    color: Colors.white70,
                                    fontSize: 14,
                                    fontWeight: FontWeight.bold,
                                    decoration: TextDecoration.underline,
                                    decorationThickness: 1.2,
                                  )),
                                ),
                              )
                            ],
                          ),
                        ),
                      ]),
                ),
              ),
            ),
          )
        ],
      ),
    );
	//...sign up
}
</Widget>
</code></pre>

<p><strong>2. Let us implement our sign in.</strong></p>
<pre><div id="copy-button36" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>@override
	Widget build(BuildContext context) {
	//...divider
	//...sign in
	var signUpPage = Container(
      alignment: Alignment.center,
      margin: EdgeInsets.symmetric(horizontal: 30, vertical: 0),
      child: Column(
        mainAxisSize: MainAxisSize.max,
        mainAxisAlignment: MainAxisAlignment.center,
        crossAxisAlignment: CrossAxisAlignment.stretch,
        children: [
          Expanded(
            child: SingleChildScrollView(
              child: AbsorbPointer(
                absorbing: isLoading,
                child: Form(
                  key: _signUpFormKey,
                  child: Column(
                      children: <Widget> [
                        SizedBox(height: 50,),
                        Text("Flutter Sign In Reference", style: TextStyle(
                          fontSize: 30,
                          color: Colors.amber,
                        ),),
                        SizedBox(height: 60,),
                        TextFormField(
                          textCapitalization: TextCapitalization.words,
                          textAlignVertical: TextAlignVertical.center,
                          textAlign: TextAlign.left,
                          controller: signUpName,
                          obscureText: false,
                          autofocus: false,
                          maxLines: 1,
                          autocorrect: true,
                          textInputAction: TextInputAction.next,
                          keyboardType: TextInputType.text,
                          validator: (input) {
                            if(input.toString().length >= 3) {
                              return null;
                            } else {
                              return "Please enter your name.";
                            }
                          },
                          onFieldSubmitted: (_) => FocusScope.of(context).nextFocus(),
                          cursorColor: Colors.amber,
                          style: TextStyle(fontSize: 16, color: Colors.white,),
                          decoration: InputDecoration(
                              enabled: true,
                              prefixIcon: Icon(Icons.account_box, color: Colors.amber,size: 18,),
                              focusColor: Colors.amber,
                              contentPadding: EdgeInsets.all(10),
                              border: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              enabledBorder: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              hintText: "Enter your Name",
                              hintStyle: TextStyle(color: Colors.white54)
                          ),
                        ),
                        SizedBox(height: 30,),
                        TextFormField(
                          textCapitalization: TextCapitalization.words,
                          textAlignVertical: TextAlignVertical.center,
                          textAlign: TextAlign.left,
                          controller: signUpEmail,
                          obscureText: false,
                          autofocus: false,
                          maxLines: 1,
                          autocorrect: true,
                          textInputAction: TextInputAction.next,
                          keyboardType: TextInputType.emailAddress,
                          validator: (input) {
                            if(Validator.isValidEmail(input.toString())) {
                              return null;
                            } else {
                              return "Please enter a valid email.";
                            }
                          },
                          onFieldSubmitted: (_) => FocusScope.of(context).nextFocus(),
                          cursorColor: Colors.amber,
                          style: TextStyle(fontSize: 16, color: Colors.white,),
                          decoration: InputDecoration(
                              enabled: true,
                              prefixIcon: Icon(Icons.email, color: Colors.amber,size: 18,),
                              focusColor: Colors.amber,
                              contentPadding: EdgeInsets.all(10),
                              border: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              enabledBorder: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              hintText: "Enter your Email",
                              hintStyle: TextStyle(color: Colors.white54)
                          ),
                        ),
                        SizedBox(height: 30,),
                        TextFormField(
                          textCapitalization: TextCapitalization.words,
                          textAlignVertical: TextAlignVertical.center,
                          textAlign: TextAlign.left,
                          controller: signUpPassword,
                          obscureText: true,
                          autofocus: false,
                          maxLines: 1,
                          autocorrect: false,
                          textInputAction: TextInputAction.next,
                          keyboardType: TextInputType.text,
                          validator: (input) {
                            if(input.toString().length >= 6) {
                              return null;
                            } else {
                              return "Your password must contain at least 6 characters.";
                            }
                          },
                          cursorColor: Colors.amber,
                          style: TextStyle(fontSize: 16, color: Colors.white,),
                          decoration: InputDecoration(
                              enabled: true,
                              prefixIcon: Icon(Icons.lock, color: Colors.amber,size: 18),
                              focusColor: Colors.amber,
                              contentPadding: EdgeInsets.all(10),
                              border: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              enabledBorder: UnderlineInputBorder(
                                borderSide: BorderSide(color: Colors.amber, width: 2),
                              ),
                              hintText: "Enter your a Password",
                              hintStyle: TextStyle(color: Colors.white54)
                          ),
                        ),
                        Container(
                          margin: EdgeInsets.symmetric(horizontal: 10, vertical: 18),
                          child: Row(
                            mainAxisAlignment: MainAxisAlignment.center,
                            children: [
                              Text('Already have an account?', style: TextStyle(
                                color: Colors.white70,
                                fontSize: 14,
                              ),),
                              InkWell(
                                onTap: () => {
                                  setState(() {
                                    isLoginPage = !isLoginPage;
                                  })
                                },
                                splashColor: Colors.amber,
                                child: Container(
                                  padding: EdgeInsets.symmetric(vertical: 5, horizontal: 5),
                                  child: Text('Sign In', style: TextStyle(
                                    color: Colors.white70,
                                    fontSize: 14,
                                    fontWeight: FontWeight.bold,
                                    decoration: TextDecoration.underline,
                                    decorationThickness: 1.5,
                                  )),
                                ),
                              )
                            ],
                          ),
                        ),
                        Container(
                          margin: EdgeInsets.symmetric(horizontal: 0, vertical: 4),
                          height: 36,
                          child: FlatButton(
                            shape: RoundedRectangleBorder(
                              borderRadius: BorderRadius.circular(10.0),
                              //side: BorderSide(color: Colors.red)
                            ),
                            child: Row(
                              mainAxisAlignment: MainAxisAlignment.spaceAround,
                              children: [
                                isLoading ? SizedBox(height:22, width:22,child: CircularProgressIndicator(strokeWidth: 4,)) : Text('Sign Up'.toUpperCase(), style: TextStyle(
                                  color: Colors.white70,
                                  fontSize: 16,
                                )),
                              ]),
                            color: Colors.white24,
                            focusColor: Colors.amber,
                            splashColor: Colors.amber,
                            onPressed: () {
                              if (_signUpFormKey.currentState.validate()) {
                                setState(() {
                                  isLoading = !isLoading;
                                });
                               // requestSignUp().then((response) => (requestSignUpHandler(response)));
                              }
                            },
                          ),
                        ),
                        divider,
                        Container(
                          height: 36,
                          margin: EdgeInsets.symmetric(horizontal:0,vertical: 4),
                          child: FlatButton(
                            shape: RoundedRectangleBorder(
                              borderRadius: BorderRadius.circular(10.0),
                              //side: BorderSide(color: Colors.red)
                            ),
                            child: Row(
                              mainAxisAlignment: MainAxisAlignment.spaceAround,
                              children: [
                                Image.asset('assets/images/hw_24x24_logo.png', width: 24, height: 24),
                                Text('Sign in with HUAWEI ID', style: TextStyle(
                                  color: Colors.white,
                                  fontSize: 16,
                                ),),
                                SizedBox(width: 20,),
                              ],
                            ),
                            color: Color(0xffef484b),
                            onPressed: () {

                            },
                          ),
                        ),
                      ]),
                ),
              ),
            ),
          )
        ],
      ),
    );
}
</Widget>
</code></pre>

<aside class="special">
  <p><strong>Note: If everything be done accuretely, the complex login process with Huawei ID and email will be shown as done below:</strong></p>
</aside>
<br><img style="width: 400.00px" src="https://github.com/Onurcan-Keskin/onurcan-keskin.github.io/blob/gh-pages/assets/s3.jpg?raw=true" onclick="imageclick(src)">

</ol>