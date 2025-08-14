from statistics import mean

class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name

    def __str__(self):
        return f"{self.__class__.__name__}: {self.first_name}, {self.last_name}"


class Mentor(Person):
    def __init__(self, first_name, last_name):
        super().__init__(first_name, last_name)
        self.courses_attached = []

    def attach_course(self, course):
        if course not in self.courses_attached:
            self.courses_attached.append(course)


class Lecturer(Mentor):
    def __init__(self, first_name, last_name):
        super().__init__(first_name, last_name)
        self.grades = {}

    def calculate_average_grade(self):
        all_grades = sum([grade for grades_list in self.grades.values() for grade in grades_list])
        total_count = sum(len(grades) for grades in self.grades.values())
        return round(all_grades / total_count, 1) if total_count > 0 else 0

    def __str__(self):
        avg_grade = self.calculate_average_grade()
        return f"Имя: {self.first_name}\nФамилия: {self.last_name}\nСредняя оценка за лекции: {avg_grade:.1f}"

    def __lt__(self, other):
        return self.calculate_average_grade() < other.calculate_average_grade()

    def __gt__(self, other):
        return self.calculate_average_grade() > other.calculate_average_grade()

    def __eq__(self, other):
        return self.calculate_average_grade() == other.calculate_average_grade()


class Reviewer(Mentor):
    def __str__(self):
        return f"Имя: {self.first_name}\nФамилия: {self.last_name}"

    def review_homework(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            student.add_grade(course, grade)
        else:
            return f'Ошибка: Невозможно оценить.'


class Student(Person):
    def __init__(self, first_name, last_name, gender):
        super().__init__(first_name, last_name)
        self.gender = gender
        self.courses_in_progress = []
        self.finished_courses = []
        self.grades = {}

    def calculate_average_grade(self):
        all_grades = sum([grade for grades_list in self.grades.values() for grade in grades_list])
        total_count = sum(len(grades) for grades in self.grades.values())
        return round(all_grades / total_count, 1) if total_count > 0 else 0

    def __str__(self):
        avg_grade = self.calculate_average_grade()
        courses_in_progress_str = ', '.join(self.courses_in_progress)
        finished_courses_str = ', '.join(self.finished_courses)
        return f"Имя: {self.first_name}\nФамилия: {self.last_name}\nСредняя оценка за домашние задания: {avg_grade:.1f}\nКурсы в процессе изучения: {courses_in_progress_str}\nЗавершенные курсы: {finished_courses_str}"

    def __lt__(self, other):
        return self.calculate_average_grade() < other.calculate_average_grade()

    def __gt__(self, other):
        return self.calculate_average_grade() > other.calculate_average_grade()

    def __eq__(self, other):
        return self.calculate_average_grade() == other.calculate_average_grade()

    def add_grade(self, course, grade):
        if course in self.grades:
            self.grades[course].append(grade)
        else:
            self.grades[course] = [grade]

    def rate_lecture(self, lecturer, course, grade):
        if (
            isinstance(lecturer, Lecturer)
            and course in lecturer.courses_attached
            and course in self.courses_in_progress
        ):
            lecturer.add_grade(course, grade)
        else:
            return f'Ошибка: невозможно выставить оценку!'


# Вспомогательные функции

def average_students_grade(students, course):
    """Подсчет средней оценки за домашние задания по всему списку студентов на конкретном курсе."""
    relevant_grades = [
        grade
        for student in students
        for course_, grades in student.grades.items()
        if course_ == course
        for grade in grades
    ]
    return round(mean(relevant_grades), 1) if len(relevant_grades) > 0 else 0


def average_lecturers_grade(lecturers, course):
    """Подсчет средней оценки за лекции среди всех лекторов по указанному курсу."""
    relevant_grades = [
        grade
        for lecturer in lecturers
        for course_, grades in lecturer.grades.items()
        if course_ == course
        for grade in grades
    ]
    return round(mean(relevant_grades), 1) if len(relevant_grades) > 0 else 0


# Экземпляры классов

lecturer1 = Lecturer('Иван', 'Иванов')
lecturer1.attach_course('Python')
lecturer1.attach_course('Math')

lecturer2 = Lecturer('Сергей', 'Сергеев')
lecturer2.attach_course('Python')
lecturer2.attach_course('Physics')

reviewer1 = Reviewer('Алексей', 'Петухов')
reviewer1.attach_course('Python')
reviewer1.attach_course('C++')

reviewer2 = Reviewer('Евгений', 'Егоров')
reviewer2.attach_course('Python')
reviewer2.attach_course('JavaScript')

student1 = Student('Анна', 'Семёнова', 'жен.')
student1.courses_in_progress = ['Python', 'Git']
student1.finished_courses = ['Введение в программирование']

student2 = Student('Максим', 'Кузнецов', 'муж.')
student2.courses_in_progress = ['Python', 'Algorithms']
student2.finished_courses = ['Основы программирования']

# Заполнение оценок

student1.add_grade('Python', 9)
student1.add_grade('Python', 10)
student1.add_grade('Git', 8)

student2.add_grade('Python', 8)
student2.add_grade('Python', 9)
student2.add_grade('Algorithms', 7)

lecturer1.add_grade('Python', 9)
lecturer1.add_grade('Python', 10)
lecturer1.add_grade('Math', 8)

lecturer2.add_grade('Python', 8)
lecturer2.add_grade('Python', 9)
lecturer2.add_grade('Physics', 7)

# Испытания функций

students = [student1, student2]
lecturers = [lecturer1, lecturer2]

average_python_students_grade = average_students_grade(students, 'Python')
average_python_lecturers_grade = average_lecturers_grade(lecturers, 'Python')

print(f"\nСредняя оценка студентов по курсу 'Python': {average_python_students_grade}")
print(f"Средняя оценка лекторов по курсу 'Python': {average_python_lecturers_grade}")

# Печать результатов
for student in students:
    print("\n", student)

for lecturer in lecturers:
    print("\n", lecturer)# NUM4DZ
