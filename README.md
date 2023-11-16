# Лабораторная работа 10

Задача. Для своей схемы базы данных:

- инициализировать проект с использованием SQLAlchemy.
- определить модели для представления различных сущностей.
- произвести добавление, редактирование и удаление данных.
- выполнить запросы на выборку данных из базы данных. Схема базы данных

## Схема базы данных

![Untitled](%D0%9B%D0%B0%D0%B1%D0%BE%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%BD%D0%B0%D1%8F%20%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%2010%2020ee7a245a264674b7bd1f4493d115aa/Untitled.png)

## Листинг: Python код с использованием SQLAlchemy

```python
from sqlalchemy import create_engine, Column, Integer, String, Float, DateTime, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship, sessionmaker

Base = declarative_base()

class Client(Base):
    __tablename__ = 'clients'

    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String)
    email = Column(String)
    phone = Column(String)
    bookings = relationship('Booking', back_populates='client')
    tours = relationship('ClientTour', back_populates='client')

class Tour(Base):
    __tablename__ = 'tours'

    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String)
    description = Column(String)
    price = Column(Float)
    duration = Column(String)
    bookings = relationship('Booking', back_populates='tour')
    hotels = relationship('TourHotel', back_populates='tour')
    clients = relationship('ClientTour', back_populates='tour')

class Hotel(Base):
    __tablename__ = 'hotels'

    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String)
    address = Column(String)
    rating = Column(Integer)
    bookings = relationship('Booking', back_populates='hotel')
    tours = relationship('TourHotel', back_populates='hotel')

class Booking(Base):
    __tablename__ = 'bookings'

    id = Column(Integer, primary_key=True, autoincrement=True)
    client_id = Column(Integer, ForeignKey('clients.id'))
    tour_id = Column(Integer, ForeignKey('tours.id'))
    hotel_id = Column(Integer, ForeignKey('hotels.id'))
    date = Column(DateTime)
    number_of_guests = Column(Integer)
    client = relationship('Client', back_populates='bookings')
    tour = relationship('Tour', back_populates='bookings')
    hotel = relationship('Hotel', back_populates='bookings')

class ClientTour(Base):
    __tablename__ = 'client_tours'

    client_id = Column(Integer, ForeignKey('clients.id'), primary_key=True)
    tour_id = Column(Integer, ForeignKey('tours.id'))
    client = relationship('Client', back_populates='tours')
    tour = relationship('Tour', back_populates='clients')

class TourHotel(Base):
    __tablename__ = 'tour_hotels'

    tour_id = Column(Integer, ForeignKey('tours.id'), primary_key=True)
    hotel_id = Column(Integer, ForeignKey('hotels.id'), primary_key=True)
    tour = relationship('Tour', back_populates='hotels')
    hotel = relationship('Hotel', back_populates='tours')

# Инициализация базы данных
engine = create_engine('sqlite:///:memory:')
Base.metadata.create_all(engine)

# Создание сессии
Session = sessionmaker(bind=engine)
session = Session()

# Добавления данных
new_client = Client(name='John Doe', email='john@example.com', phone='123-456-7890')
session.add(new_client)
session.commit()

#Выборка из бд
clients = session.query(Client).all()
for client in clients:
    print(f'Client ID: {client.id}, Name: {client.name}, Email: {client.email}, Phone: {client.phone}')

#Редактирования данных
client_to_update = session.query(Client).filter_by(name='John Doe').first()
client_to_update.phone = '987-654-3210'
session.commit()

# Удаления данных
client_to_delete = session.query(Client).filter_by(name='John Doe').first()
session.delete(client_to_delete)
session.commit()
```

# Результат

![Untitled](%D0%9B%D0%B0%D0%B1%D0%BE%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%BD%D0%B0%D1%8F%20%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%2010%2020ee7a245a264674b7bd1f4493d115aa/Untitled%201.png)

![Untitled](%D0%9B%D0%B0%D0%B1%D0%BE%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%BD%D0%B0%D1%8F%20%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%2010%2020ee7a245a264674b7bd1f4493d115aa/Untitled%202.png)