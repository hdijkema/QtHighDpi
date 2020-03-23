# QtHighDpi Instructions for Windows
High DPI settings for normal Qt Applications in Windows

When working with High DPI monitors, one can get a Qt Application working on Windows using some switches that can be set before initializing the application. However, this has some problems using multiple monitors en switching between them. A workaround for this is given here.

```c++
int main(int argc, char *argv[])
{
    // This setting is not for OS X.
#ifdef Q_OS_WIN
    ::SetProcessDpiAwarenessContext(DPI_AWARENESS_CONTEXT_SYSTEM_AWARE);
    QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
#if QT_VERSION_MAJOR == 5 && QT_VERSION_MINOR >= 14
    QCoreApplication::setHighDpiScaleFactorRoundingPolicy(Qt::HighDpiScaleFactorRoundingPolicy::PassThrough);
#endif
#else
#ifdef Q_OS_LINUX // X11
    QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
#endif
#endif

    // Used for QQuickWidget, etc. --> QML Code for QtWebEngine
    QCoreApplication::setAttribute(Qt::AA_ShareOpenGLContexts);
    
    // We don't want questionmarks in the windows/dialogs titlebars
    QApplication::setAttribute(Qt::AA_DisableWindowContextHelpButton);

    // Construct the application
    QApplication *app = new QApplication(argc, argv);
    
 ```
 
One would think setting `Qt::AA_EnableHighDpiScaling` and `Qt::HighDpiScaleFactorRoundingPolicy::PassThrough` (or other) would be enough, but your Qt application (<= 5.14) won't scale properly when the monitor DPI changes and your application users need to logout/login each time the monitor changes. 

Using `::SetProcessDpiAwarenessContext(DPI_AWARENESS_CONTEXT_SYSTEM_AWARE);` helps to prevent this at the cost of some vagueness in the icons. 
