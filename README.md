# AndroidMVPAuthenticationBoilerPlate
Login/Signup app which handles boiler plate Validation logics using MVP design patterns with SQLite Database.

Why this Repo?
--------------

AndroidMVPAuthenticationBoilerPlate is the code snippets to handle Signup/Login screen flow process with SQLite in an elegant manner using MVP design pattern. The ultimate focus of this repo to handle packaging structure, class hierarchy, module structure, database handling, validation checks, page tracking, sending parcelable object, storing object as strings using gson, offline data loading,etc.


***SignupView.java***

```
package com.takeoffandroid.mvpauthentication.modules.authentication.signup;

import com.takeoffandroid.mvpauthentication.models.Authentication;

public interface SignupView {

     void onValidationSuccess(Authentication authentication);

     void onValidationError(String message);
}

```

***SignupPresenter.java***

```
package com.takeoffandroid.mvpauthentication.modules.authentication.signup;

import com.takeoffandroid.mvpauthentication.models.Authentication;

public interface SignupPresenter {

    void validateCredentials(Authentication authentication);

    void onDestroy();
}

```

***SignupPresenterImpl.java***

```

package com.takeoffandroid.mvpauthentication.modules.authentication.signup;

import android.content.Context;

import com.takeoffandroid.mvpauthentication.models.Authentication;

public class SignupPresenterImpl implements SignupPresenter, SignupInteractor.OnSignupFinishedListener {

    private final Context mContext;
    private SignupView signupView;
    private SignupInteractor signupInteractor;

    public SignupPresenterImpl(Context context,SignupView signupView) {
        this.mContext = context;
        this.signupView = signupView;
        this.signupInteractor = new SignupInteractorImpl();
    }

    @Override
    public void validateCredentials(Authentication authentication) {
        signupInteractor.signup(mContext,authentication,this);
    }

    @Override public void onDestroy() {
        signupView = null;
    }

    @Override
    public void onValidationSuccess(Authentication authentication) {

        if (signupView != null) {
            signupView.onValidationSuccess(authentication);
        }
    }

    @Override
    public void onValidationError(String message) {

        if (signupView != null) {
            signupView.onValidationError(message);
        }
    }
}

```

***SignupInteractor.java***

```

package com.takeoffandroid.mvpauthentication.modules.authentication.signup;

import android.content.Context;

import com.takeoffandroid.mvpauthentication.models.Authentication;

public interface SignupInteractor {

    interface OnSignupFinishedListener {

        void onValidationSuccess(Authentication authentication);

        void onValidationError(String message);
    }

    void signup(Context context, Authentication authentication, OnSignupFinishedListener listener);

}

```


***SignupInteractorImpl.java***

```

package com.takeoffandroid.mvpauthentication.modules.authentication.signup;

import android.content.Context;

import com.takeoffandroid.mvpauthentication.R;
import com.takeoffandroid.mvpauthentication.database.DBAccess;
import com.takeoffandroid.mvpauthentication.models.Authentication;
import com.takeoffandroid.mvpauthentication.utils.ValidationUtils;

public class SignupInteractorImpl implements SignupInteractor {


    @Override
    public void signup(Context context, Authentication authentication, OnSignupFinishedListener listener) {

        String email = authentication.getEmail();

        String password = authentication.getPassword();

        String mobile = authentication.getMobile();

        String userType = authentication.getUserType();

        boolean error = false;

        if (ValidationUtils.isNullOrEmpty(new String[]{email, password})){
            listener.onValidationError(context.getResources().getString(R.string.wrong_form_data));
            error = true;
            return;
        }


        if (!ValidationUtils.isValidMobile(mobile)){
            listener.onValidationError(context.getResources().getString(R.string.wrong_mobile));
            error = true;
            return;
        }

        if (!ValidationUtils.isValidEmail(email)){
            listener.onValidationError(context.getResources().getString(R.string.wrong_email));
            error = true;
            return;
        }

        if(DBAccess.init(context).isEmailAlreadyExists(email)){
            listener.onValidationError(context.getResources().getString(R.string.email_already_exists));
            error = true;
            return;
        }

        if (!ValidationUtils.isValidPassword(password)){
            listener.onValidationError(context.getResources().getString(R.string.wrong_password));
            error = true;
            return;
        }

        if(ValidationUtils.isNullOrEmpty(userType)){
            listener.onValidationError(context.getResources().getString(R.string.wrong_user_type));
            error = true;
            return;
        }


        if (!error){
            listener.onValidationSuccess(authentication);
            return;
        }
    }
}



```


**Signup**


<a href="http://imgur.com/Tob4A22"><img src="http://i.imgur.com/Tob4A22.gif" title="source: imgur.com" /></a>


**Login**


<a href="http://imgur.com/vcZgCS5"><img src="http://i.imgur.com/vcZgCS5.gif" title="source: imgur.com" /></a>


**Update**


<a href="http://imgur.com/yAi9Bm5"><img src="http://i.imgur.com/yAi9Bm5.gif" title="source: imgur.com" /></a>





