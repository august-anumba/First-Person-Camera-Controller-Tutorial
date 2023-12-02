# First Person Camera Controller Tutorial

This tutorial shows how to create a first person camera controller in Unity.

## 1. Creating `PlayerCam` C# Script

In your chosen scene and folder, create a new C# script by right clicking and selecting `Create` then `C# Script`.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/370bb5c7-007c-40b0-b1c2-0bfe649d0440)

We will be naming this new C# Script `PlayerCam`.

We start my making two floats for the `X` and `Y` sensitivity, we do this by writing the following two lines of code:

```.cs 
    public float sensX;
    public float sensY;
```
We will also use `Transform` for the players orientation in addition to two more floats for the `X` and `Y` rotation of the camera, as seen below:

```.cs
    public Transform orientation;

    float xRotation;
    float yRotation;
```
Before actually implimenting the rotation we need to then move into `void Start()` and ensure that that cursor stays locked to the middle of the screen and also invisible. We write the following lines of code under `void Start()`:

```.cs
    Cursor.lockState = CursorLockMode.Locked;
    Cursor.visible = false;
```
Now in `void Update()` we get the mouse input on both the `X` and `Y` and mulitiply it by the sensitivity float we created earlier:

```.cs
    float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensX;
    float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensY;
```
Then we add the `X` input to the `Y` rotation and substract the `Y` input from the `X` rotation:

```.cs
    yRotation += mouseX;
    xRotation -= mouseY;
```
While the code above may seem confusing at first, this is the way unity handles `rotations` and `inputs`.
Now we want to make sure that the player can't look up or down past 90 degrees as this is unrealistic, we achive this by clamping the `X` rotation from `-90f, 90f`:

```.cs
    xRotation = Mathf.Clamp(xRotation, -90f, 90f);
```
To then be granted the abilty to rotate the camera around both the `X` and `Y` axis and the player around only the `Y` axis we use `Quaterion.Euler` alongside `Transform` and `Orientation`:

```.cs
    transform.rotation = Quaternion.Euler(xRotation, yRotation, 0);
    orientation.rotation = Quaternion.Euler(0, yRotation, 0);
```
Your finished C# Script for `PlayerCam` should look like this:

```.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerCam : MonoBehaviour
{
    public float sensX;
    public float sensY;

    public Transform orientation;

    float xRotation;
    float yRotation;

    void Start()
    {
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;

    }

    void Update()
    {
        float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensX;
        float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensY;

        yRotation += mouseX;
        xRotation -= mouseY;

        xRotation = Mathf.Clamp(xRotation, -90f, 90f);

        transform.rotation = Quaternion.Euler(xRotation, yRotation, 0);
        orientation.rotation = Quaternion.Euler(0, yRotation, 0);
    }
}

```


## 2. Unity Player GameObject set-up

We now need to connect various GameObjects together as parent and children as well as attaching the C# Script we just created to a GameObject along with future C# Scripts to other GameObjects.

Firstly create 4 empty `GameObject`'s and 1 `Capsule` by right clicking inside the `Hierachy` tab, located by default in the top left, to create and empty GameObject select `Create Empty`, repeat 4 times. To create a Capsule you simply select `3D Object` and select `Capsule`.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/0f380093-f929-4f30-9684-0f051665505c)
-------------------------------

With our first empty GameObject we rename it to `CameraHolder` and rename the `Main Camera` to `PlayerCam` and drag it into `CameraHolder`. This makes the `CameraHolder` GameObject the parent and the `PlayerCam` should be sitting under it as a child as seen below:

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/dfab7d75-c9b9-4aa8-a364-22acca7a5bc0)
-------------------------------

Then with one empty GameObject, we rename it to `Player` and drag the remaining two empty `GameObject's` and the one `Capsule` into the `Player` GameObject, making `Player` the parent of them.

We we will also rename the `Capsule` to `PlayerObj` as this will act as our player's visable character, Rename one of the last two remaining empty `GameObject` to  `Orientation` and the other to `CameraPos`. It should all now look like this:

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/892a7e91-1bdd-4560-86b3-955495e8f3f0)
-------------------------------

Selecting the `Player` GameObject we then add the `Rigidbody` Component by clicking the `Add Component` button in the `Inspector` tab on the rightside of unity (while in the default layout). Then searching `Rigidbody` and selecting it.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/1c7e353b-6d45-45da-95a8-681a72a9759d)
-------------------------------

Ensure that the `Rigidbody`'s `Interpolate` is set to `Interpolate` with `Collision Detection` set to `Continuous` and select all the `X` `Y` and `Z` axis under `Constraints` and `Freeze Rotation` as seen below:

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/8a9a4e6f-07c0-4e91-9484-26b9534ce246)
-------------------------------

To explain what some of these GameObject's are used for the `Orientation` `GameObject` will be used to store plyer orientation data and `CameraPos` similarly stores the cameras positional data.

## 3. Creating `MoveCamera` C# Script

We also need to add one more simple script to make the camera always move with the player, create a new C# script by right clicking and selecting `Create` then `C# Script`.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/370bb5c7-007c-40b0-b1c2-0bfe649d0440)

We will be naming this new C# Script `MoveCamera`.

The only lines of code we need to write is:

```.cs
    public Transform cameraPosition;
```
and in `void Update()`:
```.cs
    transform.position = cameraPosition.position;
```
Your complete C# Script should look like this:

```.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MoveCamera : MonoBehaviour
{
    public Transform cameraPosition;

    void Start()
    {
        
    }

    void Update()
    {
        transform.position = cameraPosition.position;
    }
}

```
## 2. Attaching C# Scripts to `GameObjects`

We now attach the `MoveCamera` C# Script to `CameraHolder`, we do this by selecting the `CameraHolder` GameObject and dragging the `Camera Position` script into the `Inspector` (on the right in default layout) or selecting `Add Component` and searching `MoveCamera`. Inside the same `Inspector` we set the `Camera Position` box to the `CameraPos` GameObject either by dragging `CameraPos` script into it or selecting it in the drop down menu. End product:

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/b78177ed-3f88-4f62-9189-e1bdccfa0ec3)

We now attach the `PlayerCam` C# Script to `PlayerCam` GameObject, we do this by selecting the `PlayerCam` GameObject in the `Hierarchy` and dragging the `PlayerCam` script into the `Inspector` (on the right in default layout) or selecting `Add Component` and searching `PlayerCam`. Inside the same `Inspector` we set both `Sens X` and `Sens Y` to something like `400`(adjust to your preferance), while setting the `Orientation` box to the `Orientation` GameObject either by dragging `Orientation` GameObject into it or selecting it in the drop down menu. End product:

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/a2f8c284-4d69-4482-8175-016b0aef5217)

You can now test the scene you have built yourself, or by running the demo scene provided.
