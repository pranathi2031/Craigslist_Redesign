<style>
  footer {
    background:linear-gradient(125deg, rgb(85, 26, 139), rgb(206 68 164));
    color: white;
    padding: 2rem;
    text-align: center;
    
    
} 





    /* Flex container for footer lists */
    .list-container {
        display: flex;
        justify-content: center;
        gap: 30px; /* Adjust spacing between lists */
        flex-wrap: wrap; /* Wrap columns on smaller screens */
        max-width: 800px;
        margin: 0 auto;
    }

    /* Titles in each list */
    .list-container h3 {
        font-size: 1.2rem;
        margin-bottom: 0.5rem;
        text-transform: uppercase;
    }

    /* Individual lists */
    .list {
        list-style-type: none;
        padding: 0;
        margin: 0;
        text-align: left;
    }

    /* Footer links */
    .list a {
        color: white;
        text-decoration: none;
        display: inline-block;
        margin: 0.2rem 0;
        transition: color 0.3s;
    }

    /* Hover effect for links */
    .list a:hover {
        color: #f1c40f; /* Hover color */
    }

    /* Footer copyright text */
    .footer-bottom {
        margin-top: 1rem;
        font-size: 0.9rem;
        color: #ccc;
    }

    /* Responsive design for smaller screens */
    @media (max-width: 600px) {
        .list-container {
            flex-direction: column;
            align-items: center;
        }

        .list {
            text-align: center;
        }
    }
</style>

<footer>
    <div class="list-container">
        <!-- About Section -->
        <ul class="list">
            <h3>About Us</h3>
            <li><a href="https://www.craigslist.org/about/">About</a></li>
            <li><a href="https://www.craigslist.org/about/best/all/">Best</a></li>
            <li><a href="https://www.craigslist.org/about/craigslist_is_hiring">Jobs/Career</a></li>
            <li><a href="https://www.craigslist.org/about/whats-new">Latest News</a></li>
            <li><a href="https://www.craigslist.org/about/help/system-status">System Status</a></li>
            <li><a href="https://www.craigslistfund.org/">Charity</a></li>
            <li><a href="https://craignewmarkphilanthropies.org/">Newmark Philanthropies</a></li>
        </ul>

        <!-- Help Section -->
        <ul class="list">
            <h3>Support</h3>
            <li><a href="https://www.craigslist.org/about/help/">Help</a></li>
            <li><a href="https://www.craigslist.org/about/help/safety/scams/">Scams & Fraud</a></li>
            <li><a href="https://www.craigslist.org/about/help/safety/">Personal Safety</a></li>
        </ul>
    </div>

    <p class="footer-bottom">© 2024 Craigslist redesign prototype</p>
</footer>
