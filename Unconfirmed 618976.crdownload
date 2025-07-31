# Cross-platform mobile app using Kivy (Python Framework)
# Backend should be handled separately (Firebase or Django REST API)
# This is the client-side core logic (simplified version)

from kivy.app import App
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button
from datetime import datetime, timedelta
import smtplib
import random
import time

# Dummy in-memory database (replace with Firebase/SQL backend)
USERS = {}
REQUESTS = []
VERIFICATION_CODES = {}

class RegisterScreen(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        layout = BoxLayout(orientation='vertical')

        self.phone = TextInput(hint_text='Phone Number')
        self.first_name = TextInput(hint_text='First Name')
        self.last_name = TextInput(hint_text='Last Name')
        self.email = TextInput(hint_text='Email Address')
        self.code_input = TextInput(hint_text='Enter verification code')

        self.status = Label(text='')

        self.send_code_btn = Button(text='Send Verification Code')
        self.send_code_btn.bind(on_press=self.send_code)

        self.register_btn = Button(text='Register')
        self.register_btn.bind(on_press=self.register)

        layout.add_widget(self.phone)
        layout.add_widget(self.first_name)
        layout.add_widget(self.last_name)
        layout.add_widget(self.email)
        layout.add_widget(self.send_code_btn)
        layout.add_widget(self.code_input)
        layout.add_widget(self.register_btn)
        layout.add_widget(self.status)
        self.add_widget(layout)

    def send_code(self, instance):
        email = self.email.text
        code = str(random.randint(100000, 999999))
        VERIFICATION_CODES[email] = code

        # Simulate email (use real email setup in production)
        print(f"Sending code {code} to {email}")
        self.status.text = f"Verification code sent to {email}"

    def register(self, instance):
        phone = self.phone.text
        email = self.email.text
        code = self.code_input.text

        if phone in USERS:
            self.status.text = "Phone number already registered"
            return

        if email not in VERIFICATION_CODES or VERIFICATION_CODES[email] != code:
            self.status.text = "Invalid or missing verification code"
            return

        USERS[phone] = {
            'name': self.first_name.text,
            'last_name': self.last_name.text,
            'email': email,
            'rating': [],
            'blocked': False,
            'friends': set()
        }
        self.status.text = "Registration successful"
        self.manager.current = 'main'

class MainScreen(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        layout = BoxLayout(orientation='vertical')

        self.help_btn = Button(text='Request Pickup/Drop-off')
        self.help_btn.bind(on_press=self.create_request)

        self.status = Label(text='Welcome!')
        layout.add_widget(self.help_btn)
        layout.add_widget(self.status)
        self.add_widget(layout)

    def create_request(self, instance):
        now = datetime.now()
        req = {
            'poster': '1234567890',  # simulate current user phone
            'location': 'current_gps_coords',
            'status': 'open',
            'created_at': now,
            'accepted_by': None
        }
        REQUESTS.append(req)
        self.status.text = "Help request posted"

class HelpApp(App):
    def build(self):
        sm = ScreenManager()
        sm.add_widget(RegisterScreen(name='register'))
        sm.add_widget(MainScreen(name='main'))
        return sm

if __name__ == '__main__':
    HelpApp().run()
