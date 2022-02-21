[![Pipeline](https://gitlab.com/bolderflight/software/navigation/badges/main/pipeline.svg)](https://gitlab.com/bolderflight/software/navigation/) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

![Bolder Flight Systems Logo](img/logo-words_75.png) &nbsp; &nbsp; ![Arduino Logo](img/arduino_logo_75.png)

# Navigation

   * [License](LICENSE.md)
   * [Changelog](CHANGELOG.md)
   * [Contributing guide](CONTRIBUTING.md)

# Description

# Installation

## Arduino


## CMake

# Namespace
This library is within the namespace *bfs*.

# Transformations

## Position
These functions convert between:
   * Latitude, longitude, and altitude above the WGS84 ellipsoid (LLA)
   * Earth Centered Earth Fixed (ECEF)
   * Locally tangent North East Down (NED)

**Eigen::Vector3d lla2ecef(const Eigen::Vector3d &lla, const AngPosUnit ang = AngPosUnit::DEG)** Convert geodetic coordinates to Earth-centered Earth-fixed (ECEF) coordinates. Inputs are latitude, longitude, and altitude [m] above the WGS84 ellipsoid. Optionally, the units of latitude and longitude can be specified; by default, they are in degrees. Outputs are ECEF coordinates [m].

```C++
Eigen::Vector3d lla = {0, 45, 1000};
Eigen::Vector3d ecef = bfs::lla2ecef(lla);
```

**Eigen::Vector3d ecef2lla(const Eigen::Vector3d &ecef, const AngPosUnit ang = AngPosUnit::DEG)** Convert Earth-centered Earth-fixed (ECEF) coordinates to geodetic coordinates. Inputs are ECEF coordinates [m] and, optionally, the units for the output latitude and longitude; by default they are in degrees. Uses [Olson's method](https://ieeexplore.ieee.org/abstract/document/481290), which is iteration-free, very fast, and accurate.

```C++
Eigen::Vector3d ecef = {4510731, 4510731, 0};
Eigen::Vector3d lla = bfs::ecef2lla(ecef);
```

**Eigen::Vector3d ecef2ned(const Eigen::Vector3d &ecef, const Eigen::Vector3d &lla0, const AngPosUnit ang = AngPosUnit::DEG)** Rotates an Earth Centered Earth Fixed (ECEF) vector into a locally tangent North East Down (NED) frame. Inputs are the ECEF vector and the origin of the NED frame, given as latitude, longitude, and altitude [m] above the WGS84 ellipsoid. Optionally, the units of latitude and longitude can be specified; by default, they are in degrees.

```C++
Eigen::Vector3d ecef = {4510731, 4510731, 0};
Eigen::Vector3d lla0 = {0, 45, 999.956};
Eigen::Vector3d ned = bfs::ecef2ned(ecef, lla0);
```

**Eigen::Vector3d ned2ecef(const Eigen::Vector3d &ned, const Eigen::Vector3d &lla_ref, const AngPosUnit ang = AngPosUnit::DEG)** Rotates a North East Down (NED) vector into Earth Centered Earth Fixed (ECEF). Inputs are the NED vector and the origin of the NED frame, given as latitude, longitude, and altitude [m] above the WGS84 ellipsoid. Optionally, the units of latitude and longitude can be specified; by default, they are in degrees.

```C++
Eigen::Vector3d ned = {20, 20, 0};
Eigen::Vector3d lla0 = {0, 45, 999.956};
Eigen::Vector3d ecef = bfs::ned2ecef(ned, lla0);
```

**Eigen::Vector3d lla2ned(const Eigen::Vector3d &lla, const Eigen::Vector3d &lla0, const AngPosUnit ang = AngPosUnit::DEG)** Transforms geodetic coordinates to locally tangent North East Down (NED) coordinates. Inputs are the latitude, longitude, and altitude [m] above the WGS84 ellipsoid of the coordinates and latitude, longitude, and altitude [m] above the WGS84 ellipsoid of the origin of the NED frame. Optionally, the units of latitude and longitude can be specified; by default, they are in degrees.

```C++
Eigen::Vector3d lla0 = {46.017, 7.750, 1673};
Eigen::Vector3d lla = {45.976, 7.658, 4531};
Eigen::Vector3d ned = bfs::lla2ned(lla, lla0);
```

**Eigen::Vector3d ned2lla(const Eigen::Vector3d &ned, const Eigen::Vector3d &lla0, const AngPosUnit ang = AngPosUnit::DEG)** Transforms local North East Down (NED) coordinates to geodetic coordinates. Inputs are the local NED coordinates and the latitude, longitude, and altitude [m] above the WGS84 ellipsoid of the origin of the NED frame. Optionally, the units of latitude and longitude can be specified; by default, they are in degrees.

```C++
Eigen::Vector3d lla0 = {46.017, 7.750, 1673};
Eigen::Vector3d ned = {-4556.3, -7134.8, -2852.4};
Eigen::Vector3d lla = bfs::ned2lla(ned, lla0);
```

## Attitude
These functions convert between:
   * Rotation angle series
   * Euler angles ('ZYX' order rotation angles, i.e. yaw, pitch, roll)
   * Direction cosine matrix
   * Quaternions (i, j, k, w) 

**Eigen::Matrix<T, 3, 3> angle2dcm(const T rot1, const T rot2, const T rot3, const AngPosUnit ang = AngPosUnit::RAD)** Converts a series of rotation angles to a direction cosine matrix. Inputs are the three rotation angles, and optionally, the units of those angles. By default, angle input is expected in radians. Templated by floating point type.

```C++
float yaw = 0.7854;
float pitch = 0.1;
float roll = 0;
Eigen::Matrix3f dcm = bfs::angle2dcm(yaw, pitch, roll);
```

**Eigen::Matrix<T, 3, 3> eul2dcm(const Eigen::Matrix<T, 3, 1> &eul, const AngPosUnit ang = AngPosUnit::RAD)** Converts a given set of Euler angles to a direction cosine matrix. Input is the Euler angle vector (order 'ZYX') and optionally, the units of those angles. By default, angle input is expected in radians. Templated by floating point type.

```C++
float yaw = 0.7854;
float pitch = 0.1;
float roll = 0;
Eigen::Vector3f eul = {yaw, pitch, roll};
Eigen::Matrix3f dcm = bfs::eul2dcm(eul);
```

**Eigen::Matrix<T, 3, 1> dcm2angle(const Eigen::Matrix<T, 3, 3> &dcm, const AngPosUnit ang = AngPosUnit::RAD)** Creates a series of rotation angles from direction cosine matrix. Inputs are the direction cosine matrix, and optionally, the units of the output rotation angles. By default the angles are output in radians. Templated by floating point type.

```C++
Eigen::Matrix3f dcm;
dcm << 0.85253103550038,  0.47703040785184, -0.21361840626067,
      -0.43212157513194,  0.87319830445628,  0.22537893734811,
       0.29404383655186, -0.09983341664683,  0.95056378592206;
Eigen::Vector3f ang = bfs::dcm2angle(dcm);
```

**Eigen::Matrix<T, 3, 1> dcm2eul(const Eigen::Matrix<T, 3, 3> &dcm, const AngPosUnit ang = AngPosUnit::RAD)** Creates a set of Euler angles (order 'ZYX') from direction cosine matrix. Inputs are the direction cosine matrix, and optionally, the units of the output Euler angles. By default the angles are output in radians. Templated by floating point type. Functionally equivalent to *dcm2angle*.

```C++
Eigen::Matrix3f dcm;
dcm << 0.85253103550038,  0.47703040785184, -0.21361840626067,
      -0.43212157513194,  0.87319830445628,  0.22537893734811,
       0.29404383655186, -0.09983341664683,  0.95056378592206;
Eigen::Vector3f ang = bfs::dcm2eul(dcm);
```

**Eigen::Quaternion<T> angle2quat(const T rot1, const T rot2, const T rot3, const AngPosUnit a = AngPosUnit::RAD)** Convert rotation angles to quaternion. Inputs are the three rotation angles, and optionally, the units of those angles. By default, angle input is expected in radians. Templated by floating point type.

```C++
float yaw = 0.7854; 
float pitch = 0.1; 
float roll = 0;
Eigen::Quaternionf q = bfs::angle2quat(yaw, pitch, roll);
```

**Eigen::Quaternion<T> eul2quat(const T rot1, const T rot2, const T rot3, const AngPosUnit a = AngPosUnit::RAD)** Converts a given set of Euler angles to quaternion. Input is the Euler angle vector (order 'ZYX'), and optionally, the units of those angles. By default, angle input is expected in radians. Templated by floating point type.

```C++
float yaw = 0.7854; 
float pitch = 0.1; 
float roll = 0;
Eigen::Vector3f eul = {yaw, pitch, roll};
Eigen::Quaternionf q = bfs::eul2quat(eul);
```

**Eigen::Matrix<T, 3, 1> quat2angle(const Eigen::Quaternion<T> &q, const AngPosUnit ang = AngPosUnit::RAD)** Converts a quaternion to a series of rotation angles. Input is the quaternion, and optionally, the units of the output rotation angles. By default the angles are output in radians. Templated by floating point type.

```C++
Eigen::Quaternionf q;
q.x() = 0.215509;
q.y() = 0.432574;
q.z() = 0.0846792;
q.w() = 0.871358;
Eigen::Vector3f ang = bfs::quat2angle(q);
```

**Eigen::Matrix<T, 3, 1> quat2eul(const Eigen::Quaternion<T> &q, const AngPosUnit ang = AngPosUnit::RAD)** Converts a quaternion to Euler angles. Input is the quaternion, and optionally, the units of the output Euler angles. By default the angles are output in radians. Templated by floating point type. Functionally equivalent to *quat2angle*.

```C++
Eigen::Quaternionf q;
q.x() = 0.215509;
q.y() = 0.432574;
q.z() = 0.0846792;
q.w() = 0.871358;
Eigen::Vector3f ang = bfs::quat2eul(q);
```

**Eigen::Quaternion<T> dcm2quat(const Eigen::Matrix<T, 3, 3> &dcm)** Convert direction cosine matrix to quaternion. Input is the direction cosine matrix and output is the quaternion. Templated by floating point type.

```C++
Eigen::Matrix3f dcm;
dcm << 0.4330,  0.2500, -0.8660,
       0.1768,  0.9186,  0.3536,
       0.8839, -0.3062,  0.3536;
Eigen::Quaternionf q = bfs::dcm2quat(dcm);
```

**Eigen::Matrix<T, 3, 3> quat2dcm(const Eigen::Quaternion<T> &q)** Convert quaternion to direction cosine matrix. Input is the quaternion and output is the direction cosine matrix. Templated by floating point type.

```C++
Eigen::Quaternionf q;
q.x() = 0.200578;
q.y() = 0.531966;
q.z() = 0.0222526;
q.w() = 0.822375;
Eigen::Matrix3f dcm = bfs::quat2dcm(q);
```

# Utilities

**T WrapTo2Pi(T ang)** Converts a +/-PI radian angle to a 0 to 2PI angle.

**T WrapToPi(T ang)** Converts a 0 to 2PI radian angle to a +/-PI angle.
