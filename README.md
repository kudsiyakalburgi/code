################## USER MANAGEMENT ################### ################ PYTHON FILE ##############

from flask import Flask, render_template, redirect, url_for, request, session, flash from werkzeug.security import generate_password_hash, check_password_hash

app = Flask(name) app.secret_key = 'your_secret_key' # Change this to a random secret key

In-memory storage for user data
users = {}

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): if request.method == 'POST': name = request.form['name'] email = request.form['email'] password = request.form['password']

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html')
@app.route('/login', methods=['GET', 'POST']) def login(): if request.method == 'POST': email = request.form['email'] password = request.form['password']

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html')
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking') def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

return render_template('booking.html', user=users[session['user']])
if name == 'main': app.run(debug=True)

############### HTML ############

<title>Home</title>
Welcome to the Home Page
Register | Login | Booking
########## REGISTERING IN HTML ##############

<title>Register</title>
Register
Name:

Email:

Password:


Already have an account? Login
################# LOGIN HTML #####################

<title>Login</title>
Login
Email:

Password:


Don't have an account? Register
############# BOOKING HTML ###########################

<title>Booking</title>
Booking Page
Welcome, {{ user['name'] }}!

Logout
############################### BOOKING SYSTEM ############### Here is the extended Flask application:

################# python ###################

from flask import Flask, render_template, redirect, url_for, request, session, flash from flask_wtf import FlaskForm from wtforms import StringField, PasswordField, SubmitField, IntegerField, SelectField from wtforms.validators import DataRequired, Email, EqualTo, NumberRange from werkzeug.security import generate_password_hash, check_password_hash

app = Flask(name) app.secret_key = 'your_secret_key'

In-memory storage for user data and movies
users = {} movies = [ {'id': 1, 'title': 'Movie A', 'genre': 'Action', 'showtimes': ['10:00 AM', '1:00 PM', '4:00 PM']}, {'id': 2, 'title': 'Movie B', 'genre': 'Comedy', 'showtimes': ['11:00 AM', '2:00 PM', '5:00 PM']}, {'id': 3, 'title': 'Movie C', 'genre': 'Drama', 'showtimes': ['12:00 PM', '3:00 PM', '6:00 PM']} ] bookings = []

class RegistrationForm(FlaskForm): name = StringField('Name', validators=[DataRequired()]) email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')]) submit = SubmitField('Register')

class LoginForm(FlaskForm): email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) submit = SubmitField('Login')

class BookingForm(FlaskForm): movie_id = SelectField('Movie', choices=[], coerce=int, validators=[DataRequired()]) showtime = SelectField('Showtime', choices=[], validators=[DataRequired()]) seats = IntegerField('Number of Seats', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Book')

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): form = RegistrationForm() if form.validate_on_submit(): name = form.name.data email = form.email.data password = form.password.data

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html', form=form)
@app.route('/login', methods=['GET', 'POST']) def login(): form = LoginForm() if form.validate_on_submit(): email = form.email.data password = form.password.data

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html', form=form)
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking', methods=['GET', 'POST']) def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

form = BookingForm()
form.movie_id.choices = [(movie['id'], movie['title']) for movie in movies]
form.showtime.choices = []

if request.method == 'POST' and form.validate_on_submit():
    movie_id = form.movie_id.data
    showtime = form.showtime.data
    seats = form.seats.data
    
    movie = next(movie for movie in movies if movie['id'] == movie_id)
    bookings.append({
        'user': session['user'],
        'movie': movie['title'],
        'showtime': showtime,
        'seats': seats
    })
    
    flash('Booking successful')
    return redirect(url_for('confirmation', movie_id=movie_id, showtime=showtime, seats=seats))

return render_template('booking.html', form=form, movies=movies)
@app.route('/confirmation') def confirmation(): if 'user' not in session: flash('Please log in to access the confirmation page') return redirect(url_for('login'))

movie_id = request.args.get('movie_id')
showtime = request.args.get('showtime')
seats = request.args.get('seats')

movie = next(movie for movie in movies if movie['id'] == int(movie_id))
return render_template('confirmation.html', movie=movie, showtime=showtime, seats=seats)
if name == 'main': app.run(debug=True) HTML Templates:

################ home.html ###################

html

<title>Home</title>
Welcome to the Home Page
Register | Login | Booking ################# register.html ##############
html

<title>Register</title>
Register
{{ form.hidden_tag() }} {{ form.name.label }}
{{ form.name }}
{{ form.email.label }}
{{ form.email }}
{{ form.password.label }}
{{ form.password }}
{{ form.confirm_password.label }}
{{ form.confirm_password }}

{{ form.submit }} Already have an account? Login ######### login.html ###########
html

<title>Login</title>
Login
{{ form.hidden_tag() }} {{ form.email.label }}
{{ form.email }}
{{ form.password.label }}
{{ form.password }}

{{ form.submit }} Don't have an account? Register ################# booking.html #############
html

<title>Booking</title> <script> function updateShowtimes() { var movieId = document.getElementById("movie_id").value; var showtimes = { {% for movie in movies %} "{{ movie.id }}": ["{{ movie.showtimes|join('","') }}"], {% endfor %} };
        var showtimeSelect = document.getElementById("showtime");
        showtimeSelect.innerHTML = "";

        showtimes[movieId].forEach(function(showtime) {
            var option = document.createElement("option");
            option.text = showtime;
            option.value = showtime;
            showtimeSelect.add(option);
        });
    }
</script>
Book Your Tickets
{{ form.hidden_tag() }} {{ form.movie_id.label }}
{{ form.movie_id(id='movie_id', onchange="updateShowtimes()") }}
{{ form.showtime.label }}
{{ form.showtime(id='showtime') }}
{{ form.seats.label }}
{{ form.seats }}

{{ form.submit }}
##########confirmation.html ########### html

<title>Booking Confirmation</title>
Booking Confirmation
Movie: {{ movie['title'] }}

Showtime: {{ showtime }}

Seats: {{ seats }}

Total Price: ${{ seats * 10 }}

Thank you for your booking!

##################### SEATING ARRANGEMENT ######################## ############## python ###############

from flask import Flask, render_template, redirect, url_for, request, session, flash, jsonify from flask_wtf import FlaskForm from wtforms import StringField, PasswordField, SubmitField, IntegerField, SelectField from wtforms.validators import DataRequired, Email, EqualTo, NumberRange from werkzeug.security import generate_password_hash, check_password_hash from flask_socketio import SocketIO, emit, join_room, leave_room import eventlet import time import threading

app = Flask(name) app.secret_key = 'your_secret_key' socketio = SocketIO(app)

In-memory storage for user data, movies, bookings, and seat states
users = {} movies = [ {'id': 1, 'title': 'Movie A', 'genre': 'Action', 'showtimes': ['10:00 AM', '1:00 PM', '4:00 PM']}, {'id': 2, 'title': 'Movie B', 'genre': 'Comedy', 'showtimes': ['11:00 AM', '2:00 PM', '5:00 PM']}, {'id': 3, 'title': 'Movie C', 'genre': 'Drama', 'showtimes': ['12:00 PM', '3:00 PM', '6:00 PM']} ] bookings = [] seat_states = { '1_10:00 AM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '1_1:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '1_4:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '2_11:00 AM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '2_2:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '2_5:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '3_12:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '3_3:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, '3_6:00 PM': {'available': set(range(1, 101)), 'locked': {}, 'booked': set()}, }

class RegistrationForm(FlaskForm): name = StringField('Name', validators=[DataRequired()]) email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')]) submit = SubmitField('Register')

class LoginForm(FlaskForm): email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) submit = SubmitField('Login')

class BookingForm(FlaskForm): movie_id = SelectField('Movie', choices=[], coerce=int, validators=[DataRequired()]) showtime = SelectField('Showtime', choices=[], validators=[DataRequired()]) seats = IntegerField('Number of Seats', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Book')

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): form = RegistrationForm() if form.validate_on_submit(): name = form.name.data email = form.email.data password = form.password.data

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html', form=form)
@app.route('/login', methods=['GET', 'POST']) def login(): form = LoginForm() if form.validate_on_submit(): email = form.email.data password = form.password.data

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html', form=form)
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking', methods=['GET', 'POST']) def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

form = BookingForm()
form.movie_id.choices = [(movie['id'], movie['title']) for movie in movies]
form.showtime.choices = []

if request.method == 'POST' and form.validate_on_submit():
    movie_id = form.movie_id.data
    showtime = form.showtime.data
    seats = form.seats.data
    
    movie = next(movie for movie in movies if movie['id'] == movie_id)
    session['current_booking'] = {
        'movie_id': movie_id,
        'showtime': showtime,
        'seats': seats
    }
    return redirect(url_for('seat_selection'))

return render_template('booking.html', form=form, movies=movies)
@app.route('/seat-selection', methods=['GET', 'POST']) def seat_selection(): if 'user' not in session or 'current_booking' not in session: flash('Please log in and select a movie to access the seat selection page') return redirect(url_for('login'))

current_booking = session['current_booking']
movie_id = current_booking['movie_id']
showtime = current_booking['showtime']
key = f"{movie_id}_{showtime}"
available_seats = seat_states[key]['available']
locked_seats = seat_states[key]['locked'].get(session['user'], set())
all_locked_seats = set()
for seats in seat_states[key]['locked'].values():
    all_locked_seats.update(seats)

return render_template('seat_selection.html', available_seats=available_seats, locked_seats=locked_seats, all_locked_seats=all_locked_seats, total_seats=100)
@socketio.on('lock_seat') def handle_lock_seat(data): user = session['user'] current_booking = session['current_booking'] movie_id = current_booking['movie_id'] showtime = current_booking['showtime'] seat_number = data['seat'] key = f"{movie_id}_{showtime}"

if seat_number in seat_states[key]['available']:
    seat_states[key]['available'].remove(seat_number)
    if user not in seat_states[key]['locked']:
        seat_states[key]['locked'][user] = set()
    seat_states[key]['locked'][user].add(seat_number)
    emit('seat_locked', {'seat': seat_number, 'user': user}, broadcast=True)
    
    def release_seat(seat_number, key):
        time.sleep(300)  # 5 minutes lock duration
        if seat_number in seat_states[key]['locked'].get(user, set()):
            seat_states[key]['locked'][user].remove(seat_number)
            seat_states[key]['available'].add(seat_number)
            emit('seat_released', {'seat': seat_number, 'user': user}, broadcast=True)

    threading.Thread(target=release_seat, args=(seat_number, key)).start()
@socketio.on('unlock_seat') def handle_unlock_seat(data): user = session['user'] current_booking = session['current_booking'] movie_id = current_booking['movie_id'] showtime = current_booking['showtime'] seat_number = data['seat'] key = f"{movie_id}_{showtime}"

if seat_number in seat_states[key]['locked'].get(user, set()):
    seat_states[key]['locked'][user].remove(seat_number)
    seat_states[key]['available'].add(seat_number)
    emit('seat_unlocked', {'seat': seat_number, 'user': user}, broadcast=True)
@app.route('/confirm-booking', methods=['POST']) def confirm_booking(): if 'user' not in session or 'current_booking' not in session: flash('Please log in and select a movie to access the confirmation page') return redirect(url_for('login'))

current_booking = session['current_booking']
user = session['user']
movie_id = current_booking['movie_id']
showtime = current_booking['showtime']
key
############# SCREEN CONFIGURATION ###################### ############### python #####################

from flask import Flask, render_template, redirect, url_for, request, session, flash, jsonify from flask_wtf import FlaskForm from wtforms import StringField, PasswordField, SubmitField, IntegerField, SelectField from wtforms.validators import DataRequired, Email, EqualTo, NumberRange from werkzeug.security import generate_password_hash, check_password_hash from flask_socketio import SocketIO, emit import eventlet import time import threading

app = Flask(name) app.secret_key = 'your_secret_key' socketio = SocketIO(app)

In-memory storage for user data, movies, bookings, and seat states
users = {} movies = [ {'id': 1, 'title': 'Movie A', 'genre': 'Action', 'showtimes': ['10:00 AM', '1:00 PM', '4:00 PM'], 'screens': [1, 2]}, {'id': 2, 'title': 'Movie B', 'genre': 'Comedy', 'showtimes': ['11:00 AM', '2:00 PM', '5:00 PM'], 'screens': [1]}, {'id': 3, 'title': 'Movie C', 'genre': 'Drama', 'showtimes': ['12:00 PM', '3:00 PM', '6:00 PM'], 'screens': [2]} ] screen_capacity = {1: 60, 2: 60} bookings = [] seat_states = { '1_10:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_10:00 AM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_11:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_2:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_5:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_12:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_3:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_6:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()} }

class RegistrationForm(FlaskForm): name = StringField('Name', validators=[DataRequired()]) email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')]) submit = SubmitField('Register')

class LoginForm(FlaskForm): email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) submit = SubmitField('Login')

class BookingForm(FlaskForm): movie_id = SelectField('Movie', choices=[], coerce=int, validators=[DataRequired()]) showtime = SelectField('Showtime', choices=[], validators=[DataRequired()]) screen = SelectField('Screen', choices=[], coerce=int, validators=[DataRequired()]) seats = IntegerField('Number of Seats', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Book')

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): form = RegistrationForm() if form.validate_on_submit(): name = form.name.data email = form.email.data password = form.password.data

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html', form=form)
@app.route('/login', methods=['GET', 'POST']) def login(): form = LoginForm() if form.validate_on_submit(): email = form.email.data password = form.password.data

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html', form=form)
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking', methods=['GET', 'POST']) def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

form = BookingForm()
form.movie_id.choices = [(movie['id'], movie['title']) for movie in movies]
form.showtime.choices = []
form.screen.choices = []

if request.method == 'POST' and form.validate_on_submit():
    movie_id = form.movie_id.data
    showtime = form.showtime.data
    screen = form.screen.data
    seats = form.seats.data
    
    movie = next(movie for movie in movies if movie['id'] == movie_id)
    session['current_booking'] = {
        'movie_id': movie_id,
        'showtime': showtime,
        'screen': screen,
        'seats': seats
    }
    return redirect(url_for('seat_selection'))

return render_template('booking.html', form=form, movies=movies)
@app.route('/seat-selection', methods=['GET', 'POST']) def seat_selection(): if 'user' not in session or 'current_booking' not in session: flash('Please log in and select a movie to access the seat selection page') return redirect(url_for('login'))

current_booking = session['current_booking']
movie_id = current_booking['movie_id']
showtime = current_booking['showtime']
screen = current_booking['screen']
key = f"{movie_id}_{showtime}_{screen}"
available_seats = seat_states[key]['available']
locked_seats = seat_states[key]['locked'].get(session['user'], set())
all_locked_seats = set()
for seats in seat_states[key]['locked'].values():
    all_locked_seats.update(seats)

return render_template('seat_selection.html', available_seats=available_seats, locked_seats=locked_seats, all_locked_seats=all_locked_seats, total_seats=screen_capacity[screen])
@socketio.on('lock_seat') def handle_lock_seat(data): user = session['user'] current_booking = session['current_booking'] movie_id = current_booking['movie_id'] showtime = current_booking['showtime'] screen = current_booking['screen'] seat_number = data['seat'] key = f"{movie_id}{showtime}{screen}"

if seat_number in seat_states[key]['available']:
    seat_states[key]['available'].remove(seat_number)
    if user not in seat_states[key]['locked']:
        seat_states[key]['locked'][user] = set()
    seat_states[key]['locked'][user].add(seat_number)
    emit('seat_locked', {'seat': seat_number, 'user': user}, broadcast=True)
    
    def release_seat(seat_number, key):
        time.sleep(300)  # 5 minutes lock duration
        if seat_number in seat_states[key]['locked'].get(user, set()):
            seat_states[key]['locked'][user].remove(seat_number)
            seat_states[key]['available'].add(seat_number)
            emit('seat_released', {'seat': seat_number, 'user': user}, broadcast=True)

    threading.Thread(target=release_seat, args=(seat_number, key)).start()
@socketio.on('unlock_seat') def handle_unlock_seat(data): user = session['user'] current_booking = session['current_booking'] movie_id = current_booking['movie_id'] showtime = current_booking['showtime'] screen = current_booking['screen'] seat_number = data['seat'] key = f"{movie_id}{showtime}{screen}"

if seat_number in seat_states[key]['locked
############## ADMIN PANEL #################### python

from flask import Flask, render_template, redirect, url_for, request, session, flash, jsonify from flask_wtf import FlaskForm from wtforms import StringField, PasswordField, SubmitField, IntegerField, SelectField, TextAreaField from wtforms.validators import DataRequired, Email, EqualTo, NumberRange from werkzeug.security import generate_password_hash, check_password_hash from flask_socketio import SocketIO, emit import eventlet import time import threading

app = Flask(name) app.secret_key = 'your_secret_key' socketio = SocketIO(app)

In-memory storage for user data, movies, bookings, screens, seat states, and food menu
users = {'admin@example.com': {'name': 'Admin', 'password': generate_password_hash('adminpass', method='sha256'), 'is_admin': True}} movies = [ {'id': 1, 'title': 'Movie A', 'genre': 'Action', 'showtimes': ['10:00 AM', '1:00 PM', '4:00 PM'], 'screens': [1, 2]}, {'id': 2, 'title': 'Movie B', 'genre': 'Comedy', 'showtimes': ['11:00 AM', '2:00 PM', '5:00 PM'], 'screens': [1]}, {'id': 3, 'title': 'Movie C', 'genre': 'Drama', 'showtimes': ['12:00 PM', '3:00 PM', '6:00 PM'], 'screens': [2]} ] screens = {1: 60, 2: 60} bookings = [] seat_states = { '1_10:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_10:00 AM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_11:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_2:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_5:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_12:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_3:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_6:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()} } food_menu = []

class RegistrationForm(FlaskForm): name = StringField('Name', validators=[DataRequired()]) email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')]) submit = SubmitField('Register')

class LoginForm(FlaskForm): email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) submit = SubmitField('Login')

class BookingForm(FlaskForm): movie_id = SelectField('Movie', choices=[], coerce=int, validators=[DataRequired()]) showtime = SelectField('Showtime', choices=[], validators=[DataRequired()]) screen = SelectField('Screen', choices=[], coerce=int, validators=[DataRequired()]) seats = IntegerField('Number of Seats', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Book')

class ScreenManagementForm(FlaskForm): screen_id = IntegerField('Screen ID', validators=[DataRequired(), NumberRange(min=1)]) capacity = IntegerField('Capacity', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Update Screen')

class FoodMenuForm(FlaskForm): item_name = StringField('Item Name', validators=[DataRequired()]) description = TextAreaField('Description', validators=[DataRequired()]) price = IntegerField('Price', validators=[DataRequired(), NumberRange(min=0)]) submit = SubmitField('Add Item')

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): form = RegistrationForm() if form.validate_on_submit(): name = form.name.data email = form.email.data password = form.password.data

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password, 'is_admin': False}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html', form=form)
@app.route('/login', methods=['GET', 'POST']) def login(): form = LoginForm() if form.validate_on_submit(): email = form.email.data password = form.password.data

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html', form=form)
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking', methods=['GET', 'POST']) def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

form = BookingForm()
form.movie_id.choices = [(movie['id'], movie['title']) for movie in movies]
form.showtime.choices = []
form.screen.choices = []

if request.method == 'POST' and form.validate_on_submit():
    movie_id = form.movie_id.data
    showtime = form.showtime.data
    screen = form.screen.data
    seats = form.seats.data
    
    movie = next(movie for movie in movies if movie['id'] == movie_id)
    session['current_booking'] = {
        'movie_id': movie_id,
        'showtime': showtime,
        'screen': screen,
        'seats': seats
    }
    return redirect(url_for('seat_selection'))

return render_template('booking.html', form=form, movies=movies)
@app.route('/seat-selection', methods=['GET', 'POST']) def seat_selection(): if 'user' not in session or 'current_booking' not in session: flash('Please log in and select a movie to access the seat selection page') return redirect(url_for('login'))

current_booking = session['current_booking']
movie_id = current_booking['movie_id']
showtime = current_booking['showtime']
screen = current_booking['screen']
key = f"{movie_id}_{showtime}_{screen}"
available_seats = seat_states[key]['available']
locked_seats = seat_states[key]['locked'].get(session['user'], set())
all_locked_seats = set()
for seats in seat_states[key]['locked'].values():
    all_locked_seats.update(seats)

return render_template('seat_selection.html', available_seats=available_seats, locked_seats=locked_seats, all_locked_seats=all_locked_seats, total_seats=screens[screen])
@socketio.on('lock_seat') def handle_lock_seat(data): user = session['user'] current_booking = session['current_booking'] movie_id = current_booking['movie_id'] showtime = current_booking['showtime'] screen = current_booking['screen'] seat_number = data['seat'] key = f"{movie_id}{showtime}{screen}"

if seat_number in seat_states[key]['available']:
    seat_states[key]['available'].remove(seat_number)
    if user not in seat_states[key]['locked']:
        seat_states[key]['locked'][user] = set()
    seat_states[key]['locked'][user].add(seat_number)
    emit('seat_locked', {'seat': seat
#################### NOTIFICATION ############# python

from flask import Flask, render_template, redirect, url_for, request, session, flash, jsonify from flask_wtf import FlaskForm from wtforms import StringField, PasswordField, SubmitField, IntegerField, SelectField, TextAreaField from wtforms.validators import DataRequired, Email, EqualTo, NumberRange from werkzeug.security import generate_password_hash, check_password_hash from flask_socketio import SocketIO, emit from flask_mail import Mail, Message import eventlet import time import threading

app = Flask(name) app.secret_key = 'your_secret_key' socketio = SocketIO(app) mail = Mail(app)

Configure Flask-Mail
app.config['MAIL_SERVER'] = 'smtp.example.com' app.config['MAIL_PORT'] = 587 app.config['MAIL_USE_TLS'] = True app.config['MAIL_USERNAME'] = 'your-email@example.com' app.config['MAIL_PASSWORD'] = 'your-email-password' app.config['MAIL_DEFAULT_SENDER'] = 'your-email@example.com'

In-memory storage for user data, movies, bookings, screens, seat states, and food menu
users = {'admin@example.com': {'name': 'Admin', 'password': generate_password_hash('adminpass', method='sha256'), 'is_admin': True}} movies = [ {'id': 1, 'title': 'Movie A', 'genre': 'Action', 'showtimes': ['10:00 AM', '1:00 PM', '4:00 PM'], 'screens': [1, 2]}, {'id': 2, 'title': 'Movie B', 'genre': 'Comedy', 'showtimes': ['11:00 AM', '2:00 PM', '5:00 PM'], 'screens': [1]}, {'id': 3, 'title': 'Movie C', 'genre': 'Drama', 'showtimes': ['12:00 PM', '3:00 PM', '6:00 PM'], 'screens': [2]} ] screens = {1: 60, 2: 60} bookings = [] seat_states = { '1_10:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_10:00 AM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_11:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_2:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_5:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_12:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_3:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_6:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()} } food_menu = []

class RegistrationForm(FlaskForm): name = StringField('Name', validators=[DataRequired()]) email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')]) submit = SubmitField('Register')

class LoginForm(FlaskForm): email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) submit = SubmitField('Login')

class BookingForm(FlaskForm): movie_id = SelectField('Movie', choices=[], coerce=int, validators=[DataRequired()]) showtime = SelectField('Showtime', choices=[], validators=[DataRequired()]) screen = SelectField('Screen', choices=[], coerce=int, validators=[DataRequired()]) seats = IntegerField('Number of Seats', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Book')

class ScreenManagementForm(FlaskForm): screen_id = IntegerField('Screen ID', validators=[DataRequired(), NumberRange(min=1)]) capacity = IntegerField('Capacity', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Update Screen')

class FoodMenuForm(FlaskForm): item_name = StringField('Item Name', validators=[DataRequired()]) description = TextAreaField('Description', validators=[DataRequired()]) price = IntegerField('Price', validators=[DataRequired(), NumberRange(min=0)]) submit = SubmitField('Add Item')

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): form = RegistrationForm() if form.validate_on_submit(): name = form.name.data email = form.email.data password = form.password.data

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password, 'is_admin': False}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html', form=form)
@app.route('/login', methods=['GET', 'POST']) def login(): form = LoginForm() if form.validate_on_submit(): email = form.email.data password = form.password.data

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html', form=form)
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking', methods=['GET', 'POST']) def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

form = BookingForm()
form.movie_id.choices = [(movie['id'], movie['title']) for movie in movies]
form.showtime.choices = []
form.screen.choices = []

if request.method == 'POST' and form.validate_on_submit():
    movie_id = form.movie_id.data
    showtime = form.showtime.data
    screen = form.screen.data
    seats = form.seats.data
    
    movie = next(movie for movie in movies if movie['id'] == movie_id)
    session['current_booking'] = {
        'movie_id': movie_id,
        'showtime': showtime,
        'screen': screen,
        'seats': seats
    }
    return redirect(url_for('seat_selection'))

return render_template('booking.html', form=form, movies=movies)
@app.route('/seat-selection', methods=['GET', 'POST']) def seat_selection(): if 'user' not in session or 'current_booking' not in session: flash('Please log in and select a movie to access the seat selection page') return redirect(url_for('login'))

current_booking = session['current_booking']
movie_id = current_booking['movie_id']
showtime = current_booking['showtime']
screen = current_booking['screen']
key = f"{movie_id}_{showtime}_{screen}"
available_seats = seat_states[key]['available']
locked_seats = seat_states[key]['locked'].get(session['user'], set())
all_locked_seats = set()
for seats in seat_states[key]['locked'].values():
    all_locked_seats.update(seats)

return render_template('seat_selection.html', available_seats=available_seats, locked_seats=locked_seats, all_locked_seats=all_locked_seats, total_seats=screens[screen])
def send_email(to, subject, body): msg = Message(subject, recipients=[to], body=body) mail.send(msg)

@socketio.on('lock_seat') def handle_lock_seat(data): user = session['user'] current_booking = session['current_booking'] movie_id = current_booking['movie_id'] showtime = current_booking['showtime'] screen = current_booking['

########################## SECURITY ######################### python

from flask import Flask, render_template, redirect, url_for, request, session, flash, jsonify from flask_wtf import FlaskForm from wtforms import StringField, PasswordField, SubmitField, IntegerField, SelectField, TextAreaField from wtforms.validators import DataRequired, Email, EqualTo, NumberRange from werkzeug.security import generate_password_hash, check_password_hash from flask_socketio import SocketIO, emit from flask_mail import Mail, Message import eventlet import time import threading import os import base64 from cryptography.fernet import Fernet

app = Flask(name) app.secret_key = os.urandom(24) socketio = SocketIO(app) mail = Mail(app)

Configure Flask-Mail
app.config['MAIL_SERVER'] = 'smtp.example.com' app.config['MAIL_PORT'] = 587 app.config['MAIL_USE_TLS'] = True app.config['MAIL_USERNAME'] = 'your-email@example.com' app.config['MAIL_PASSWORD'] = 'your-email-password' app.config['MAIL_DEFAULT_SENDER'] = 'your-email@example.com'

Generate a key for encryption
encryption_key = Fernet.generate_key() cipher_suite = Fernet(encryption_key)

In-memory storage for user data, movies, bookings, screens, seat states, and food menu
users = {'admin@example.com': {'name': 'Admin', 'password': generate_password_hash('adminpass', method='sha256'), 'is_admin': True}} movies = [ {'id': 1, 'title': 'Movie A', 'genre': 'Action', 'showtimes': ['10:00 AM', '1:00 PM', '4:00 PM'], 'screens': [1, 2]}, {'id': 2, 'title': 'Movie B', 'genre': 'Comedy', 'showtimes': ['11:00 AM', '2:00 PM', '5:00 PM'], 'screens': [1]}, {'id': 3, 'title': 'Movie C', 'genre': 'Drama', 'showtimes': ['12:00 PM', '3:00 PM', '6:00 PM'], 'screens': [2]} ] screens = {1: 60, 2: 60} bookings = [] seat_states = { '1_10:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_10:00 AM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_1:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '1_4:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_11:00 AM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_2:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '2_5:00 PM_1': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_12:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_3:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()}, '3_6:00 PM_2': {'available': set(range(1, 61)), 'locked': {}, 'booked': set()} } food_menu = []

class RegistrationForm(FlaskForm): name = StringField('Name', validators=[DataRequired()]) email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')]) submit = SubmitField('Register')

class LoginForm(FlaskForm): email = StringField('Email', validators=[DataRequired(), Email()]) password = PasswordField('Password', validators=[DataRequired()]) submit = SubmitField('Login')

class BookingForm(FlaskForm): movie_id = SelectField('Movie', choices=[], coerce=int, validators=[DataRequired()]) showtime = SelectField('Showtime', choices=[], validators=[DataRequired()]) screen = SelectField('Screen', choices=[], coerce=int, validators=[DataRequired()]) seats = IntegerField('Number of Seats', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Book')

class ScreenManagementForm(FlaskForm): screen_id = IntegerField('Screen ID', validators=[DataRequired(), NumberRange(min=1)]) capacity = IntegerField('Capacity', validators=[DataRequired(), NumberRange(min=1)]) submit = SubmitField('Update Screen')

class FoodMenuForm(FlaskForm): item_name = StringField('Item Name', validators=[DataRequired()]) description = TextAreaField('Description', validators=[DataRequired()]) price = IntegerField('Price', validators=[DataRequired(), NumberRange(min=0)]) submit = SubmitField('Add Item')

@app.route('/') def home(): return render_template('home.html')

@app.route('/register', methods=['GET', 'POST']) def register(): form = RegistrationForm() if form.validate_on_submit(): name = form.name.data email = form.email.data password = form.password.data

    if email in users:
        flash('Email address already registered')
        return redirect(url_for('register'))
    
    hashed_password = generate_password_hash(password, method='sha256')
    users[email] = {'name': name, 'password': hashed_password, 'is_admin': False}
    
    flash('Registration successful, please log in.')
    return redirect(url_for('login'))

return render_template('register.html', form=form)
@app.route('/login', methods=['GET', 'POST']) def login(): form = LoginForm() if form.validate_on_submit(): email = form.email.data password = form.password.data

    user = users.get(email)
    if user and check_password_hash(user['password'], password):
        session['user'] = email
        flash('Login successful')
        return redirect(url_for('booking'))
    else:
        flash('Invalid email or password')

return render_template('login.html', form=form)
@app.route('/logout') def logout(): session.pop('user', None) flash('You have been logged out') return redirect(url_for('home'))

@app.route('/booking', methods=['GET', 'POST']) def booking(): if 'user' not in session: flash('Please log in to access the booking page') return redirect(url_for('login'))

form = BookingForm()
form.movie_id.choices = [(movie['id'], movie['title']) for movie in movies]
form.showtime.choices = []
form.screen.choices = []

if request.method == 'POST' and form.validate_on_submit():
    movie_id = form.movie_id.data
    showtime = form.showtime.data
    screen = form.screen.data
    seats = form.seats.data
    
    movie = next(movie for movie in movies if movie['id'] == movie_id)
    session['current_booking'] = {
        'movie_id': movie_id,
        'showtime': showtime,
        'screen': screen,
        'seats': seats
    }
    return redirect(url_for('seat_selection'))

return render_template('booking.html', form=form, movies=movies)
@app.route('/seat-selection', methods=['GET', 'POST']) def seat_selection(): if 'user' not in session or 'current_booking' not in session: flash('Please log in and select a movie to access the seat selection page') return redirect(url_for('login'))

current_booking = session['current_booking']
movie_id = current_booking['movie_id']
showtime = current_booking['showtime']
screen = current_booking['screen']
key = f"{movie_id}_{showtime}_{screen}"
available_seats = seat_states[key]['available']
locked_seats = seat_states[key]['locked'].get(session['user'], set())
all_locked_seats = set()
for seats in seat_states[key]['locked'].values():
    all_locked_seats.update(seats)

return render_template('seat_selection.html', available_seats=available_seats, locked_seats=locked_seats, all_locked_seats=all_locked_seats, total_seats=screens[screen])
def send_email(to, subject, body): msg = Message(subject, recipients=[to], body=body) mail.send(msg)

@socketio.on('lock_seat') def handle_lock_seat(data): user = session['user']

################## DEPLOYMENT ############# ############### TXT ################### Flask==2.0.2 Flask-Mail==0.9.1 Flask-SocketIO==5.1.1 cryptography==3.4.7 eventlet==0.31.0

############### YAML ##################### services:

type: web name: movie-booking-backend env: python plan: free buildCommand: "" startCommand: "python app.py" autoDeploy: true envVars:
key: SECRET_KEY value: your_secret_key
key: MAIL_SERVER value: smtp.example.com
key: MAIL_PORT value: 587
key: MAIL_USERNAME value: your-email@example.com
key: MAIL_PASSWORD value: your-email-password
############## JS #################### import axios from 'axios';

const api = axios.create({ baseURL: 'https://your-backend-url.onrender.com', });

const fetchMovies = async () => { try { const response = await api.get('/movies'); console.log(response.data); } catch (error) { console.error('Error fetching movies:', error); } };

fetchMovies();
