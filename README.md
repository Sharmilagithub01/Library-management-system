import json

class Book:
    def _init_(self, title, author, copies):
        self.title = title
        self.author = author
        self.copies = copies

    def to_dict(self):
        return {
            'title': self.title,
            'author': self.author,
            'copies': self.copies
        }

class User:
    def _init_(self, name):
        self.name = name
        self.borrowed_books = []

    def to_dict(self):
        return {
            'name': self.name,
            'borrowed_books': self.borrowed_books
        }

class Library:
    def _init_(self):
        self.books = []
        self.users = []

    def add_book(self, title, author, copies):
        book = Book(title, author, copies)
        self.books.append(book)
        print(f"Book '{title}' by {author} added to the library.")

    def remove_book(self, title):
        for book in self.books:
            if book.title == title:
                self.books.remove(book)
                print(f"Book '{title}' removed from the library.")
                return
        print(f"Book '{title}' not found in the library.")

    def borrow_book(self, user_name, title):
        user = self._find_user(user_name)
        if not user:
            print(f"User '{user_name}' not found.")
            return

        for book in self.books:
            if book.title == title:
                if book.copies > 0:
                    book.copies -= 1
                    user.borrowed_books.append(title)
                    print(f"Book '{title}' borrowed by {user_name}.")
                    return
                else:
                    print(f"No copies of '{title}' are currently available.")
                    return
        print(f"Book '{title}' not found in the library.")

    def return_book(self, user_name, title):
        user = self._find_user(user_name)
        if not user:
            print(f"User '{user_name}' not found.")
            return

        if title in user.borrowed_books:
            user.borrowed_books.remove(title)
            for book in self.books:
                if book.title == title:
                    book.copies += 1
                    print(f"Book '{title}' returned by {user_name}.")
                    return
        print(f"Book '{title}' not borrowed by {user_name}.")

    def display_books(self):
        if not self.books:
            print("No books available in the library.")
            return
        for book in self.books:
            print(f"Title: {book.title}, Author: {book.author}, Copies: {book.ccopies}")

    def add_user(self, name):
        user = User(name)
        self.users.append(user)
        print(f"User '{name}' added.")

    def display_users(self):
        if not self.users:
            print("No users in the library.")
            return
        for user in self.users:
            print(f"User: {user.name}, Borrowed Books: {', '.join(user.borrowed_books) if user.borrowed_books else 'None'}")

    def _find_user(self, name):
        for user in self.users:
            if user.name == name:
                return user
        return None

    def save_to_file(self, filename):
        data = {
            'books': [book.to_dict() for book in self.books],
            'users': [user.to_dict() for user in self.users]
        }
        with open(filename, 'w') as file:
            json.dump(data, file)
        print(f"Library data saved to {filename}.")

    def load_from_file(self, filename):
        try:
            with open(filename, 'r') as file:
                data = json.load(file)
                self.books = [Book(**book_data) for book_data in data['books']]
                self.users = [User(**user_data) for user_data in data['users']]
            print(f"Library data loaded from {filename}.")
        except FileNotFoundError:
            print(f"File {filename} not found.")
