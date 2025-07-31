# Django REST API for Help Request App
# Backend features: registration, email verification, help requests, rating, friend system, location tracking

# Directory structure:
# helpapp/
#   └── api/
#        ├── models.py
#        ├── views.py
#        ├── serializers.py
#        ├── urls.py
#   └── settings.py
#   └── urls.py

# models.py
from django.db import models
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager
from django.utils import timezone

class UserManager(BaseUserManager):
    def create_user(self, phone_number, email, first_name, last_name):
        if not phone_number or not email:
            raise ValueError("Users must have a phone number and email")
        user = self.model(phone_number=phone_number, email=email,
                          first_name=first_name, last_name=last_name)
        user.set_unusable_password()
        user.save(using=self._db)
        return user

class User(AbstractBaseUser):
    phone_number = models.CharField(unique=True, max_length=15)
    email = models.EmailField(unique=True)
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
    is_verified = models.BooleanField(default=False)
    rating = models.FloatField(default=5.0)
    bad_reviews = models.IntegerField(default=0)
    blocked = models.BooleanField(default=False)
    friends = models.ManyToManyField('self', blank=True)

    USERNAME_FIELD = 'phone_number'
    REQUIRED_FIELDS = ['email', 'first_name', 'last_name']

    objects = UserManager()

    def __str__(self):
        return f"{self.first_name} {self.last_name} ({self.phone_number})"

class VerificationCode(models.Model):
    email = models.EmailField()
    code = models.CharField(max_length=6)
    created_at = models.DateTimeField(default=timezone.now)

class HelpRequest(models.Model):
    requester = models.ForeignKey(User, related_name='requests_made', on_delete=models.CASCADE)
    helper = models.ForeignKey(User, related_name='requests_accepted', null=True, blank=True, on_delete=models.SET_NULL)
    status = models.CharField(max_length=20, choices=[('open', 'Open'), ('accepted', 'Accepted'), ('declined', 'Declined'), ('completed', 'Completed')], default='open')
    created_at = models.DateTimeField(auto_now_add=True)
    location = models.CharField(max_length=200)
    latitude = models.FloatField(null=True, blank=True)
    longitude = models.FloatField(null=True, blank=True)
    description = models.TextField(blank=True)

    def is_expired(self):
        return timezone.now() - self.created_at > timezone.timedelta(hours=24)

class Rating(models.Model):
    rater = models.ForeignKey(User, related_name='ratings_given', on_delete=models.CASCADE)
    ratee = models.ForeignKey(User, related_name='ratings_received', on_delete=models.CASCADE)
    score = models.IntegerField()  # 1 to 5
    feedback = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def save(self, *args, **kwargs):
        super().save(*args, **kwargs)
        if self.score <= 1:
            self.ratee.bad_reviews += 1
            if self.ratee.bad_reviews >= 20:
                self.ratee.blocked = True
            self.ratee.save()

# serializers.py
from rest_framework import serializers
from .models import User, HelpRequest, VerificationCode, Rating

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'phone_number', 'email', 'first_name', 'last_name', 'is_verified', 'rating', 'bad_reviews', 'blocked', 'friends']

class HelpRequestSerializer(serializers.ModelSerializer):
    class Meta:
        model = HelpRequest
        fields = '__all__'

class VerificationCodeSerializer(serializers.ModelSerializer):
    class Meta:
        model = VerificationCode
        fields = '__all__'

class RatingSerializer(serializers.ModelSerializer):
    class Meta:
        model = Rating
        fields = '__all__'

# views.py
from rest_framework import viewsets, status
from rest_framework.response import Response
from rest_framework.decorators import action
from .models import User, HelpRequest, VerificationCode, Rating
from .serializers import UserSerializer, HelpRequestSerializer, VerificationCodeSerializer, RatingSerializer
from django.core.mail import send_mail
from django.utils.crypto import get_random_string

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=False, methods=['post'])
    def verify_email(self, request):
        email = request.data.get('email')
        code = get_random_string(length=6, allowed_chars='0123456789')
        VerificationCode.objects.create(email=email, code=code)
        send_mail('Your Verification Code', f'Code: {code}', 'noreply@helpline.com', [email])
        return Response({'status': 'verification code sent'})

    @action(detail=False, methods=['post'])
    def confirm_code(self, request):
        email = request.data.get('email')
        code = request.data.get('code')
        if VerificationCode.objects.filter(email=email, code=code).exists():
            user = User.objects.filter(email=email).first()
            if user:
                user.is_verified = True
                user.save()
            return Response({'status': 'email verified'})
        return Response({'status': 'invalid code'}, status=status.HTTP_400_BAD_REQUEST)

    @action(detail=True, methods=['post'])
    def add_friend(self, request, pk=None):
        user = self.get_object()
        friend_id = request.data.get('friend_id')
        try:
            friend = User.objects.get(id=friend_id)
            user.friends.add(friend)
            return Response({'status': 'friend added'})
        except User.DoesNotExist:
            return Response({'error': 'Friend not found'}, status=404)

class HelpRequestViewSet(viewsets.ModelViewSet):
    queryset = HelpRequest.objects.all()
    serializer_class = HelpRequestSerializer

class RatingViewSet(viewsets.ModelViewSet):
    queryset = Rating.objects.all()
    serializer_class = RatingSerializer

# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import UserViewSet, HelpRequestViewSet, RatingViewSet

router = DefaultRouter()
router.register(r'users', UserViewSet)
router.register(r'helprequests', HelpRequestViewSet)
router.register(r'ratings', RatingViewSet)

urlpatterns = [
    path('', include(router.urls))
]
