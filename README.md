# Setpoint_twist-navstack_pub
study more : 
> 1. http://wiki.ros.org/move_base
>
> 2. http://wiki.ros.org/move_base_msgs

Ex1. Set move 1 meter forrward
```c++
#include <ros/ros.h>
#include <move_base_msgs/MoveBaseAction.h>
#include <actionlib/client/simple_action_client.h>
#include <iostream>
 
using namespace std;

typedef actionlib::SimpleActionClient<move_base_msgs::MoveBaseAction> MoveBaseClient;

int main(int argc, char** argv){
  ros::init(argc, argv, "simple_navigation_goals");

  //tell the action client that we want to spin a thread by default
  MoveBaseClient ac("move_base", true);

  //wait for the action server to come up
  while(!ac.waitForServer(ros::Duration(5.0))){
    ROS_INFO("Waiting for the move_base action server to come up");
  }

  move_base_msgs::MoveBaseGoal goal;

  //we'll send a goal to the robot to move 1 meter forward
  goal.target_pose.header.frame_id = "base_link";
  goal.target_pose.header.stamp = ros::Time::now();

  goal.target_pose.pose.position.x = 1.0;
  goal.target_pose.pose.orientation.w = 1.0;

  ROS_INFO("Sending goal");
  ac.sendGoal(goal);

  ac.waitForResult();

  if(ac.getState() == actionlib::SimpleClientGoalState::SUCCEEDED)
    ROS_INFO("Hooray, the base moved 1 meter forward");
  else
    ROS_INFO("The base failed to move forward 1 meter for some reason");

  return 0;
}
```
E2. Set point in map
```c++

#include <ros/ros.h>
#include <move_base_msgs/MoveBaseAction.h>
#include <actionlib/client/simple_action_client.h>
#include <iostream>
 
using namespace std;
 
typedef actionlib::SimpleActionClient<move_base_msgs::MoveBaseAction> MoveBaseClient;
 
int main(int argc, char** argv){
   

  ros::init(argc, argv, "simple_navigation_goals");
 
  MoveBaseClient ac("move_base", true);
 
  while(!ac.waitForServer(ros::Duration(5.0))){
    ROS_WARN("Waiting for the move_base action server to come up");
  }
 
  int user_send = 3;
  char user_continue = 'Y';
  bool run = true; // 1
     
  while(run) {
 
    cout << "\nWhere do you want the robot to go in map?" << endl;
    cout << "\n1 = set point1" << endl;
    cout << "2 = set point2" << endl;
    cout << "\nEnter a number: ";
    cin >> user_send;
 
    move_base_msgs::MoveBaseGoal goal;
 
    goal.target_pose.header.frame_id = "map";
    goal.target_pose.header.stamp = ros::Time::now();
         
    bool valid_selection = true;
 
    switch (user_send) {
      case 1:
        cout << "\nGoal Location: set point1\n" << endl;
        goal.target_pose.pose.position.x = 2.70656919479;
        goal.target_pose.pose.position.y = 0.0;
        goal.target_pose.pose.orientation.w = 1.0;
        break;//stop when succeeded
      case 2:
        cout << "\nGoal Location: set point2\n" << endl;
        goal.target_pose.pose.position.x = 0.37883974791;
        goal.target_pose.pose.position.y = 0.517381310463;
        goal.target_pose.pose.orientation.w = 1.0;
        break;
      default:
        cout << "\nInvalid selection. Please try again.\n" << endl;
        valid_selection = false;
    }       
         
    if(!valid_selection) {
      continue;
    }
 
    ROS_INFO("Sending goal");
    ac.sendGoal(goal);
 
    ac.waitForResult();
 
    if(ac.getState() == actionlib::SimpleClientGoalState::SUCCEEDED)
      ROS_INFO("The robot has arrived at the goal location");
    else
      ROS_FATAL("The robot failed to reach the goal location for some reason");

    do {
      cout << "\nWould you like to go to another destination? (Y/N)" << endl;
      cin >> user_continue;
      user_continue = tolower(user_continue); 
    } while (user_continue != 'n' && user_continue != 'y'); 
 
    if(user_continue =='n') {
        run = false;
    }  
  }
   
  return 0;
}
```
