### 1. `AnonRateThrottle` (The "Anon" or Anonymous User)

This applies to anyone who is **not logged in**.

- **How it works:** Since the user isn't logged in, DRF cannot identify them by a unique ID (like a username). Instead, it identifies them by their **IP address**.
    
- **Why have a limit?** If you have an open API, you don't want a single person (or a malicious script) to hit your backend server 50,000 times a day. You set a lower limit (e.g., `100/day`) to prevent abuse from unauthenticated traffic.

### 2. `UserRateThrottle` (The "User" or Authenticated User)

This applies to users who have **logged into your app** (using tokens, sessions, etc.).

- **How it works:** Because the user is authenticated, DRF identifies them by their **User ID** (the unique record in your database).
    
- **Why have a limit?** Even logged-in users should have a cap. This prevents one user from accidentally (or intentionally) consuming all of your API quota. Since they are "known" users, you usually grant them a higher limit than anonymous visitors.

### Global Setup (Easiest)
``` Python
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
