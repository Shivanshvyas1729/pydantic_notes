class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14 * (self.radius ** 2)

my_circle = Circle(5)
print(my_circle.area) # Accessed as an attribute, output is 78.5
