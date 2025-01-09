# Introduction

* 修复`https://github.com/lvfengchi/livox_laser_simulation`代码中ubuntu20.04 + gazebo11无法适配问题

* 点云类型（publish_pointcloud_type）：

  | index | 对应的点云类型                                               |
  | ----- | ------------------------------------------------------------ |
  | 0     | sensor_msg::pointcloud                                       |
  | 1     | sensor_msg::pointcloud2(pcl::Pointcloud<pcl::PointXYZ>)      |
  | 2     | sensor_msg::pointcloud2(pcl::Pointcloud<pcl::LivoxPointXyzrtl>) |
  | 3     | livox_ros_driver::CustomMsg                                  |



# Usage

**可以模仿 hunter_se_avia.xacro 文件和 robot.xacro 文件**

> **在xacro文件中插入：**

```
  <link name="livox_avia">
    <!-- <collision>
      <origin xyz="0 0 0" rpy="0 0 0"/>  
      <geometry>
        <sphere radius="0.000000001" />
      </geometry>
    </collision>	 -->
    <visual>
      <origin xyz="0 0 0" rpy="0 0 0"/>
      <geometry>
      <box size="0.01 0.01 0.01"/>
      </geometry>
    </visual>
    </link>
  <joint name="laser_livox_joint" type="fixed" >
    <origin xyz="0.46 0 0.09" rpy="0 0 0" />
    <parent link="base_link" />
    <child link="livox_avia"/>
  </joint>

  <xacro:property name="laser_min_range" value="0.1"/>
  <xacro:property name="laser_max_range" value="200.0"/>
  <xacro:property name="horizontal_fov" value="70.4"/>
  <xacro:property name="vertical_fov" value="77.2"/>
  <xacro:property name="ros_topic" value="livox/lidar"/>
  <xacro:property name="samples" value="10000"/>
  <xacro:property name="downsample" value="1"/>
  <xacro:property name="csv_file_name" value="package://livox_laser_simulation/scan_mode/avia.csv"/>
  <xacro:property name="publish_pointcloud_type" value="1"/>

  <gazebo reference="livox_avia">             <!-- 为小车上某个 link -->
    <sensor type="ray" name="livox_avia">     <!-- 与上一项保持相同 -->
      <pose>0 0 0 0 0 0</pose>
      <visualize>true</visualize>
      <update_rate>10</update_rate>
      <!-- This ray plgin is only for visualization. -->
      <plugin name="gazebo_ros_laser_controller" filename="liblivox_laser_simulation.so">
        <ray>
          <scan>
          <horizontal>
          <samples>100</samples>
          <resolution>1</resolution>
          <min_angle>${-horizontal_fov/360*M_PI}</min_angle>
          <max_angle>${horizontal_fov/360*M_PI}</max_angle>
          </horizontal>
          <vertical>
          <samples>50</samples>
          <resolution>1</resolution>
          <min_angle>${-vertical_fov/360*M_PI}</min_angle>
          <max_angle>${vertical_fov/360*M_PI}</max_angle>
          </vertical>
          </scan>
          <range>
          <min>${laser_min_range}</min>
          <max>${laser_max_range}</max>
          <resolution>0.002</resolution>
          </range>
          <noise>
          <type>gaussian</type>
          <mean>0.0</mean>
          <stddev>0.01</stddev>
          </noise>
        </ray>
        <visualize>true</visualize>
        <samples>${samples}</samples>
        <downsample>${downsample}</downsample>
        <csv_file_name>${csv_file_name}</csv_file_name>
        <publish_pointcloud_type>${publish_pointcloud_type}</publish_pointcloud_type>
        <ros_topic>${ros_topic}</ros_topic>
      </plugin>
    </sensor>
    <sensor name="livox_imu" type="imu">
      <always_on>true</always_on>
      <update_rate>50</update_rate>
      <visualize>false</visualize>
      <topic>"/livox/imu"</topic>
      <plugin filename="libgazebo_ros_imu_sensor.so" name="imu_plugin">
        <topicName>/livox/imu</topicName>
        <bodyName>imu_link</bodyName>
        <updateRateHZ>10.0</updateRateHZ>
        <xyzOffset>0 0 0</xyzOffset>
        <gaussianNoise>0.0026399240948208127</gaussianNoise>
        <rpyOffset>0 0 0</rpyOffset>
        <frameName>imu_link</frameName>
        <initialOrientationAsReference>false</initialOrientationAsReference>
      </plugin>
      <pose>0.05512 0.02226 -0.0297 0 0 0</pose>
    </sensor>
  </gazebo>
```







