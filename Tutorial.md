# First Person Camera Controller Tutorial

This tutorial shows how to create a first person camera controller in Unity.

In your chosen scene, create a new C# script by right clicking and selecting Create then C# Script.

We will be naming this script `PlayerCam`

We start my making two floats for the X and Y sensitivity, we do this but writing the following two lines of code:
,,,.cs
public class PlayerCam : MonoBehaviour
{
    public float sensX;
    public float sensY;
    {
        transform.position = cameraPosition.position;
    }
}
```
