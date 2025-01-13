import random
import string
from flask import Flask, render_template, request, redirect, url_for, flash
from flask_mail import Mail, Message
import os
from dotenv import load_dotenv

load_dotenv()  # Load environment variables from .env file

app = Flask(__name__)

# Email configurations
app.config['MAIL_USERNAME'] = os.getenv('MAIL_USERNAME')
app.config['MAIL_PASSWORD'] = os.getenv('MAIL_PASSWORD')
app.config['MAIL_SERVER'] = os.getenv('MAIL_SERVER')
app.config['MAIL_PORT'] = os.getenv('MAIL_PORT')
app.config['MAIL_USE_TLS'] = os.getenv('MAIL_USE_TLS')

mail = Mail(app)

# Generate OTP function
def generate_otp(length=6):
    characters = string.digits
    otp = ''.join(random.choice(characters) for _ in range(length))
    return otp

# Route to display login page
@app.route('/')
def home():
    return render_template('login.html')

# Route to handle OTP sending
@app.route('/send-otp', methods=['POST'])
def send_otp():
    user_email = request.form['email']  # Get the email from the form
    otp = generate_otp()  # Generate the OTP
    
    # Create the OTP email
    msg = Message('Your OTP Code', 
                  sender=app.config['MAIL_USERNAME'], 
                  recipients=[user_email])
    msg.body = f'Your OTP code is: {otp}'
    
    try:
        # Send the email
        mail.send(msg)
        flash('OTP sent successfully!', 'success')
        return redirect(url_for('verify_otp', otp=otp, email=user_email))
    except Exception as e:
        flash('Error sending OTP. Please try again later.', 'error')
        return redirect(url_for('home'))

# Route to verify OTP
@app.route('/verify-otp', methods=['GET', 'POST'])
def verify_otp():
    otp = request.args.get('otp')
    email = request.args.get('email')
    
    if request.method == 'POST':
        user_otp = request.form['otp']
        if user_otp == otp:
            flash('OTP verified successfully!', 'success')
            return redirect(url_for('user_dashboard'))  # Redirect to user dashboard
        else:
            flash('Invalid OTP. Please try again.', 'error')
    
    return render_template('verify_otp.html', email=email)

# Route for the user dashboard
@app.route('/user-dashboard')
def user_dashboard():
    return render_template('user_dashboard.html')

if __name__ == "__main__":
    app.run(debug=True)
