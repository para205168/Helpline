// App.js
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import AppNavigator from './navigation/AppNavigator';

export default function App() {
  return (
    <NavigationContainer>
      <AppNavigator />
    </NavigationContainer>
  );
}

// navigation/AppNavigator.js
import React from 'react';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import RegisterScreen from '../screens/RegisterScreen';
import VerifyEmailScreen from '../screens/VerifyEmailScreen';
import HelpRequestScreen from '../screens/HelpRequestScreen';
import MapScreen from '../screens/MapScreen';
import FriendRatingScreen from '../screens/FriendRatingScreen';
import FriendListScreen from '../screens/FriendListScreen';
import PostFeedScreen from '../screens/PostFeedScreen';

const Stack = createNativeStackNavigator();

export default function AppNavigator() {
  return (
    <Stack.Navigator initialRouteName="Register">
      <Stack.Screen name="Register" component={RegisterScreen} options={{ headerShown: false }} />
      <Stack.Screen name="VerifyEmail" component={VerifyEmailScreen} options={{ title: 'Verify Email' }} />
      <Stack.Screen name="HelpRequest" component={HelpRequestScreen} options={{ title: 'Post Help Request' }} />
      <Stack.Screen name="Map" component={MapScreen} options={{ title: 'Track Location' }} />
      <Stack.Screen name="FriendRating" component={FriendRatingScreen} options={{ title: 'Friends & Ratings' }} />
      <Stack.Screen name="FriendList" component={FriendListScreen} options={{ title: 'My Friends' }} />
      <Stack.Screen name="PostFeed" component={PostFeedScreen} options={{ title: 'Help Feed' }} />
    </Stack.Navigator>
  );
}

// screens/FriendRatingScreen.js
import React, { useState, useEffect } from 'react';
import { View, Text, FlatList, Button, StyleSheet, Alert } from 'react-native';

export default function FriendRatingScreen() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetchUsers();
  }, []);

  const fetchUsers = async () => {
    try {
      const res = await fetch('http://localhost:8000/api/users/');
      const data = await res.json();
      if (res.ok) {
        setUsers(data);
      }
    } catch (err) {
      Alert.alert('Error', 'Could not fetch users.');
    }
  };

  const sendFriendRequest = async (id) => {
    try {
      const res = await fetch(`http://localhost:8000/api/friends/request/`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ to_user_id: id }),
      });
      const data = await res.json();
      if (res.ok) {
        Alert.alert('Success', 'Friend request sent');
      } else {
        Alert.alert('Error', data.message);
      }
    } catch (err) {
      Alert.alert('Error', 'Request failed.');
    }
  };

  const renderItem = ({ item }) => (
    <View style={styles.item}>
      <Text>{item.first_name} {item.last_name}</Text>
      <Text>Rating: {item.rating.toFixed(1)} / 5</Text>
      <Button title="Send Friend Request" onPress={() => sendFriendRequest(item.id)} />
    </View>
  );

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Users</Text>
      <FlatList
        data={users}
        renderItem={renderItem}
        keyExtractor={item => item.id.toString()}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16 },
  title: { fontSize: 20, fontWeight: 'bold', marginBottom: 16 },
  item: { padding: 12, borderBottomColor: '#ccc', borderBottomWidth: 1 }
});
