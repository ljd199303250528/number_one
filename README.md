# number_one
// 获取屏幕高
Point point = new Point();
getWindowManager().getDefaultDisplay().getSize(point);
int screenHeight = point.y + CommonUtils.getNavigationBarHeight(context);

/**
 * 获取虚拟按键栏高度
 * @param context
 * @return
 */
public static int getNavigationBarHeight(Context context) {
    int result = 0;
    if (hasNavBar(context)){
        Resources res = context.getResources();
        int resourceId = res.getIdentifier("navigation_bar_height", "dimen", "android");
        if (resourceId > 0) {
            result = res.getDimensionPixelSize(resourceId);
        }
    }
    return result;
}

/**
 * 检查是否存在虚拟按键栏
 * @param context
 * @return
 */
private static boolean hasNavBar(Context context) {
    Resources res = context.getResources();
    int resourceId = res.getIdentifier("config_showNavigationBar", "bool", "android");
    if (resourceId != 0) {
        boolean hasNav = res.getBoolean(resourceId);
        // check override flag
        String sNavBarOverride = getNavBarOverride();
        if ("1".equals(sNavBarOverride)) {
            hasNav = false;
        } else if ("0".equals(sNavBarOverride)) {
            hasNav = true;
        }
        return hasNav;
    } else { // fallback
        return !ViewConfiguration.get(context).hasPermanentMenuKey();
    }
}

/**
 * 判断虚拟按键栏是否重写
 * @return
 */
private static String getNavBarOverride() {
    String sNavBarOverride = null;
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
        try {
            Class c = Class.forName("android.os.SystemProperties");
            Method m = c.getDeclaredMethod("get", String.class);
            m.setAccessible(true);
            sNavBarOverride = (String) m.invoke(null, "qemu.hw.mainkeys");
        } catch (Throwable e) {
        }
    }
    return sNavBarOverride;
}
