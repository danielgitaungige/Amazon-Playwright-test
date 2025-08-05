import pytest
from playwright.sync_api import sync_playwright


def test_amazon_search():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)
        page = browser.new_page()

        page.goto("https://www.amazon.com")

        # Accept cookies if prompt appears (EU users)
        try:
            page.click("text=Accept Cookies")
        except:
            pass  # No cookie popup

        # Search for something
        page.fill("input#twotabsearchtextbox", "headphones")
        page.press("input#twotabsearchtextbox", "Enter")

        # Wait for results to load
        page.wait_for_selector("div.s-main-slot")

        # Assert results exist
        results = page.query_selector_all("div.s-main-slot div[data-component-type='s-search-result']")
        assert len(results) > 0, "No search results found."

        browser.close()

