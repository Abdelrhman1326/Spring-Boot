### 1. `AnonRateThrottle` (The "Anon" or Anonymous User)

This applies to anyone who is **not logged in**.

- **How it works:** Since the user isn't logged in, DRF cannot identify them by a unique ID (like a username). Instead, it identifies them by their **IP address**.
    
- **Why have a limit?** If you have an open API, you don't want a single person (or a malicious script) to hit your backend server 50,000 times a day. You set a lower limit (e.g., `100/day`) to prevent abuse from unauthenticated traffic.

### 2. `UserRateThrottle` (The "User" or Authenticated User)

This applies to users who have **logged into your app** (using tokens, sessions, etc.).

- **How it works:** Because the user is authenticated, DRF identifies them by their **User ID** (the unique record in your database).
    
- **Why have a limit?** Even logged-in users should have a cap. This prevents one user from accidentally (or intentionally) consuming all of your API quota. Since they are "known" users, you usually grant them a higher limit than anonymous visitors.

### Global Setup (Easiest)
``` python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
}
```
**`AnonRateThrottle`**: Limits unauthenticated users based on their IP address.
**`UserRateThrottle`**: Limits authenticated users based on their User ID.

### Per-View Setup (More Control)
If you need specific limits for certain endpoints (like a heavy AI model inference endpoint), you can override the global settings in your `views.py`.

``` python
from rest_framework.views import APIView
from rest_framework.throttling import UserRateThrottle

class StrictRateThrottle(UserRateThrottle):
    rate = '5/minute'  # Limit to 5 requests per minute

class MyHuggingFaceView(APIView):
    throttle_classes = [StrictRateThrottle]

    def get(self, request):
        # Your logic to call Hugging Face
        return Response({"data": "success"})
```


### Multiple Throttle Classes:
``` python
from rest_framework.throttling import UserRateThrottle

class UserMinuteThrottle(UserRateThrottle):
    rate = '10/minute'

class UserDayThrottle(UserRateThrottle):
    rate = '1000/day'
```

``` python
# views.py:
class MyHuggingFaceView(APIView):
    throttle_classes = [UserMinuteThrottle, UserDayThrottle]
    
    def get(self, request):
        # ... logic
```

### Custom Throttle Classes with scope variable for settings.py
In your `views.py` (or a dedicated `throttles.py` file), define the specific logic for your tiers.
``` python
from rest_framework.throttling import UserRateThrottle, AnonRateThrottle

# For logged-in users: 1000/day AND 10/minute
class UserDayRateThrottle(UserRateThrottle):
    scope = 'user_day'

class UserMinuteRateThrottle(UserRateThrottle):
    scope = 'user_minute'

# For anonymous users: 100/day AND 2/minute
class AnonDayRateThrottle(AnonRateThrottle):
    scope = 'anon_day'

class AnonMinuteRateThrottle(AnonRateThrottle):
    scope = 'anon_minute'
```
Now, map these scopes to the actual rate limits. DRF will look for these keys in `DEFAULT_THROTTLE_RATES`.
``` python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'myapp.throttles.UserDayRateThrottle',
        'myapp.throttles.UserMinuteRateThrottle',
        'myapp.throttles.AnonDayRateThrottle',
        'myapp.throttles.AnonMinuteRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'user_day': '1000/day',
        'user_minute': '10/minute',
        'anon_day': '100/day',
        'anon_minute': '2/minute',
    }
}
```
**The "Override" Trap:** You perfectly identified the pitfall: if you were to define the _same_ class multiple times or try to stack instances of the exact same class, Python would indeed just overwrite the previous definition. By creating new subclasses, you ensure that DRF sees them as unique tools in its "throttle toolkit."
``` python
'DEFAULT_THROTTLE_RATES': {
    'anon': '100/day',
    'anon': '10/min', # This overwrites the line above!
    'user': '1000/day'
}
```
