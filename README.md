# POE-MAST-PART-3
POE MAST 3
import React, { useState } from 'react';
import { View, Text, Button, StyleSheet, TouchableOpacity, Modal, FlatList, TextInput } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';

interface Course {
  name: string;
  category: string;
  price: number;
  description: string;
}

const initialCourses: Course[] = [
  { name: 'Salad', category: 'Starters', price: 10, description: '' },
  { name: 'Soup', category: 'Starters', price: 12, description: '' },
  { name: 'Steak', category: 'Mains', price: 35, description: '' },
  { name: 'Pasta', category: 'Mains', price: 30, description: '' },
  { name: 'Ice Cream', category: 'Dessert', price: 15, description: '' },
  { name: 'Cake', category: 'Dessert', price: 18, description: '' },
];

const CourseSelector: React.FC = () => {
  const [courses, setCourses] = useState<Course[]>(initialCourses);
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isMenuVisible, setMenuVisible] = useState(false);
  const [descriptions, setDescriptions] = useState<string[]>(courses.map(course => course.description));
  const [newCourse, setNewCourse] = useState<Course>({ name: '', category: '', price: 0, description: '' });

  const calculateAveragePrices = () => {
    const categories: { [key: string]: { total: number; count: number } } = {};
    for (let i = 0; i < courses.length; i++) {
      const course = courses[i];
      if (!categories[course.category]) {
        categories[course.category] = { total: 0, count: 0 };
      }
      categories[course.category].total += course.price;
      categories[course.category].count += 1;
    }

    const averages: { [key: string]: number } = {};
    for (let category in categories) {
      averages[category] = categories[category].total / categories[category].count;
    }

    return averages;
  };

  const averagePrices = calculateAveragePrices();

  const handleCourseSelect = (index: number) => {
    setCurrentIndex(index);
    setMenuVisible(false);
  };

  const handleDescriptionChange = (text: string, index: number) => {
    const newDescriptions = [...descriptions];
    newDescriptions[index] = text;
    setDescriptions(newDescriptions);
  };

  const handleAddCourse = () => {
    if (newCourse.name && newCourse.category && newCourse.price > 0) {
      setCourses((prevCourses) => [...prevCourses, newCourse]);
      setNewCourse({ name: '', category: '', price: 0, description: '' });
    }
  };

  const handleRemoveCourse = (index: number) => {
    setCourses((prevCourses) => prevCourses.filter((_, i) => i !== index));
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome to Our Restaurant</Text>
      <Text style={styles.sectionTitle}>Average Prices by Category</Text>
      
      {Object.entries(averagePrices).map(([category, average]) => (
        <Text key={category} style={styles.averagePriceText}>
          {category}: R{average.toFixed(2)}
        </Text>
      ))}

      <Text style={styles.courseText}>Current Selection: {courses[currentIndex].name}</Text>
      <Text style={styles.priceText}>Price: R{courses[currentIndex].price}</Text>

      <TextInput
        style={styles.input}
        placeholder={`Enter description for ${courses[currentIndex].name}`}
        value={descriptions[currentIndex]}
        onChangeText={(text) => handleDescriptionChange(text, currentIndex)}
      />

      <TouchableOpacity style={styles.menuButton} onPress={() => setMenuVisible(true)}>
        <Text style={styles.menuButtonText}>View Menu</Text>
      </TouchableOpacity>

      <Modal visible={isMenuVisible} transparent={true} animationType="slide">
        <View style={styles.modalContainer}>
          <View style={styles.modalContent}>
            <Text style={styles.modalTitle}>Menu</Text>
            <FlatList
              data={courses}
              keyExtractor={(item, index) => index.toString()}
              renderItem={({ item, index }) => (
                <View style={styles.courseOptionContainer}>
                  <TouchableOpacity style={styles.courseOptionText} onPress={() => handleCourseSelect(index)}>
                    <Text style={styles.courseOptionText}>{item.name}</Text>
                    <Text style={styles.courseOptionPrice}>R{item.price}</Text>
                  </TouchableOpacity>
                  <TouchableOpacity style={styles.removeButton} onPress={() => handleRemoveCourse(index)}>
                    <FontAwesome name="trash" size={18} color="white" />
                  </TouchableOpacity>
                </View>
              )}
            />
            <Button title="Close" onPress={() => setMenuVisible(false)} />
          </View>
        </View>
      </Modal>

      <TextInput
        style={styles.input}
        placeholder="Course Name"
        value={newCourse.name}
        onChangeText={(text) => setNewCourse({ ...newCourse, name: text })}
      />
      <TextInput
        style={styles.input}
        placeholder="Category"
        value={newCourse.category}
        onChangeText={(text) => setNewCourse({ ...newCourse, category: text })}
      />
      <TextInput
        style={styles.input}
        placeholder="Price"
        keyboardType="numeric"
        value={newCourse.price.toString()}
        onChangeText={(text) => setNewCourse({ ...newCourse, price: parseFloat(text) })}
      />
      <Button title="Add Course" onPress={handleAddCourse} />
    </View>
  );
};

export default CourseSelector;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#FAF3E0',
  },
  
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#8B4513',
    marginBottom: 20,
  },
  sectionTitle: {
    fontSize: 22,
    color: '#8B4513',
    marginBottom: 10,
  },
  averagePriceText: {
    fontSize: 18,
    color: '#D2691E',
    marginBottom: 5,
  },
  courseText: {
    fontSize: 22,
    color: '#8B4513',
    marginVertical: 10,
  },
  priceText: {
    fontSize: 20,
    color: '#6A5ACD',
    marginBottom: 20,
  },
  input: {
    height: 40,
    borderColor: '#8B4513',
    borderWidth: 1,
    borderRadius: 5,
    width: 250,
    marginBottom: 20,
    paddingLeft: 10,
  },
  menuButton: {
    backgroundColor: '#8B4513',
    padding: 10,
    borderRadius: 5,
    marginTop: 20,
  },
  menuButtonText: {
    color: '#FAF3E0',
    fontSize: 16,
  },
  modalContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'rgba(0, 0, 0, 0.7)',
  },
  modalContent: {
    width: '80%',
    backgroundColor: '#FAF3E0',
    padding: 20,
    borderRadius: 10,
    alignItems: 'center',
  },
  modalTitle: {
    fontSize: 24,
    color: '#8B4513',
    marginBottom: 20,
  },
  courseOptionContainer: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    padding: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#D2691E',
    width: '100%',
  },
  courseOptionText: {
    fontSize: 18,
    color: '#8B4513',
  },
  courseOptionPrice: {
    fontSize: 18,
    color: '#6A5ACD',
  },
  removeButton: {
    backgroundColor: 'red',
    padding: 5,
    borderRadius: 5,
  },
});

