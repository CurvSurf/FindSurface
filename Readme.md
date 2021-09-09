# FindSurface

**Curv*Surf* FindSurface™**

## Overview

FindSurface is a software library that extracts 3D geometric information from point cloud data.

This library supports the following platforms/languages and you can find the distribution of the library for each platform at the link:

- [iOS (Objective-C/Swift)](https://github.com/CurvSurf/FindSurface-iOS)
- [Android (Java/Kotlin)](https://github.com/CurvSurf/FindSurface-Android)
- [Windows](https://github.com/CurvSurf/FindSurface-Windows)/[Linux](https://github.com/CurvSurf/FindSurface-Linux) (C/C++)

## How does it work?

FindSurface detects a geometric model in point cloud. It starts searching with a region of interest in the points and spreading the search space until it converges to a specific mathematical representation of one of the geometric shapes according to their local curvature, which has the minimal errors in the distances to the points. The types of the shapes that FindSurface detects are planes (bounded), spheres, cylinders, cones, tori.

The strategy of FindSurface's algorithm, which affects how it spreads its search space or converges to the specific model, is determined by its parameters. The followings are the terms and its meanings that we define for the parameters:

- **Measurement Accuracy** means the *a priori* root-mean-squared error of the measurement points. In most cases, the value of this error is determined by the scanner devices that provide the points, but it may vary depending on the scan distance. You may set this value to an approximated value (about 2x of the actual error) or a heuristically estimated value. The smaller the value, the lower it gets the result's RMS error (if detected any), but lowers the algorithm's detection rate. In contrast, a larger value raises the detection rate but the result tends to have a higher RMS error.

- **Mean Distance** means an average distance between points. This value is determined by the scanner device's resolution and its scan distance. This value to be used to validate the results of FindSurface by checking the point density of inlier points. It is recommended to set this value to a 2~5 times higher value of the actual one because the inlier points will have lower point density than that of input points.

- **Seed Point (Index)** is a point of interest to start searching the surface. FindSurface accepts this information as an **index** in the point cloud.

- **Seed Radius** is the radius of a seed region around the seed point where FindSurface starts searching the surface. This value depends on the size of the geometry that you're interested in. It also had been called **Touch Radius** in the older versions of our library.

  ![touch.jpg](https://developers.curvsurf.com/Documentation/APIs/How-To/img/touch.jpg)

- **Lateral Extension** means the tendency for the searching algorithm to spread its search space in tangent direction of the surface to be sought. A larger plane or a longer cylinder might be detected as you set it to a higher value, and vice versa. Lateral extension has no influence to searching a sphere or a torus.

- **Radial Expansion** means the tendency for the searching algorithm to thicken/thin its search space in normal direction of the surface to be sought. If the measurement points are releatively accurate/inaccurate, the most/least points might be considered as inlier points as you set it to a higher/lower value (level of inlier/outlier inclusion/exclusion). In other words, it is recommended not to set it to a lower/higher value for relatively accurate/inaccurate measurement points.

- **Inlier / Outlier Flags** can be queried as an array of boolean flags from FindSurface, which tells if the point of the corresponding index should be considered to be in inliers. 

  ![inliers_outliers.jpg](https://developers.curvsurf.com/Documentation/APIs/How-To/img/inliers_outliers.jpg)

  


## What exactly do I get from FindSurface?

FindSurface produces the following information as the outputs of its algorithm:

- **RMS error** means the *posteriori* root-mean-squared error between the inlier points and the detected surface.
- **Feature Type**: the geometric type of the detected surface (plane, sphere, cylinder, cone, torus)
- **Sizes and positions** corresponding to the type:
  - The coordinates of **four corner** points of a plane (named as lower left, lower right, upper left, upper right).
  - The **center** coordinates and the **radius** of a sphere.
  - The **center** coordinates at both ends and the **radius** of a cylinder (named as top, bottom, radius).
  - The **center** coordinates and the **radii** at both ends of a cone (named as top, bottom. top radius, bottom radius).
  - The **center** coordinates and the **radius** of a torus, its **axis** vector, and the **tube radius** (named as center, mean radius, normal, tube radius).
  - The normal/axis of plane/cylinder/torus is arbitrarily chosen between two alternatives by the algorithm.
- Boolean array of which elements tells you whether the points in the corresponding indices are considered as **inlier points** by the algorithm.



## Geometric Surfaces

From the point of view of the algorithm of FindSurface, all geometric surfaces can be classified into the following five types:

- Bounded Plane (rectangle)
- Sphere
- Cylinder
- Cone (including conical frustum)
- Torus



## Auto Detection

The algorithm of FindSurface is capable of "Auto Detection", which means it can detect the most fit surface type around the seed area without specifying the feature type manually. The resulted surface type is automatically determined when the surface model converges to a specific surface type.

(TODO: local curvature에 따라 branching하는 내용 찾아서 삽입)



To use auto detection feature, set the enum value representing any type (e.g., `FS_TYPE_ANY` for C, `FeatureType::any` for Kotlin) to the feature type parameter of `findSurface` function, instead of setting a specific type.



## Smart Conversion

In the mathematical models of the geometric surfaces that the algorithm of FindSurface builds, there are some special relations between the surfaces in terms of geometric topology. For example, a cone (conical frustum) that has the same radii at both its top and bottom is actually a cylinder. After the model converges to a specific surface type, Smart conversion, if enabled, automatically converts the type according to the relations. Smart conversion converts:

- A cone (conical frustum) that has the same radii at both its top and bottom into a cylinder;
- A degenerate torus (a double-covered sphere), of which mean radius is zero, into a sphere;
  ([this gif](https://en.wikipedia.org/wiki/File:Sphere-like_degenerate_torus.gif) will help you understand what it is)
- A torus that has an infinite mean radius into a cylinder;

To enable Smart Conversion, the corresponding enum value must be set to the context manually.



## ---

(c) Copyright 2021 CurvSurf, Inc. All rights reserved.

This library's ownership is solely on CurvSurf, Inc. and anyone can use it for non-commercial purposes. Contact to support@curvsurf.com for commercial use of the library.

